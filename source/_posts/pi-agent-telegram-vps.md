---
title: 1h1g 小机上跑一个常驻 Telegram 的 AI 运维 Agent：Pi Coding Agent 实战
date: 2026-11-10 22:00:00
updated: 2026-11-18 10:00:00
categories:
  - 运维实践
tags:
  - VPS
  - AI Agent
  - Pi
  - Telegram
  - systemd
  - Debian
description: 在 1 核 1G 的 VPS 上用 Pi Coding Agent + pi-telegram 搭出常驻约 58MB、可通过 Telegram 随时调用的 AI 运维助手，并用 systemd 实现开机自启。
keywords:
  - Pi Coding Agent
  - Telegram Bot
  - 1h1g VPS
  - systemd 常驻
  - Debian 12
---

## 背景与需求

我有一台 **1 核 1G 内存**的 VPS，想在上面跑一个能常驻、能通过 Telegram 随时下指令的 AI 助手，主要用途是：

- 查状态、看日志、启停服务这类日常运维；
- 写 shell 脚本、改配置文件（需要精准的 diff / 局部替换，而不是"给我一段代码自己贴"）；
- 7×24 小时在线，手机上随时能喊。

<!-- more -->

第一反应是 Hermes Agent（Nous Research 出品）。它的自我进化闭环确实漂亮，但技术栈太重：捆绑了完整的 Python 运行时、多层记忆与向量检索、Gateway 网关。空载常驻就要 300~500MB，一开长上下文或并发就冲上 800MB+。而 Debian + SSH + systemd 本身已经吃掉 150~250MB，**结果就是被内核 OOM Killer 直接干掉**。

结论：1h1g 上需要的是编译型的轻量 Agent 运行时，而不是重型 Python 框架。

## 选型对比

调研了四个方向，量级参考如下（内存数据来源为社区资料 + 本机实测，不同版本会有差异）：

| 维度      | Hermes Agent     | ZeroClaw     | PicoClaw     | **Pi Coding Agent**             |
| :-------- | :--------------- | :----------- | :----------- | :------------------------------ |
| 底层      | Python + Node.js | Rust         | Go           | Node.js / TypeScript            |
| 空闲内存  | 300~600MB+       | 5~15MB       | 8~15MB       | **约 50~120MB**                 |
| 安装形态  | 庞大依赖包       | 单静态二进制 | 单静态二进制 | npm 全局包                      |
| 交互方式  | IM 网关为主      | IM 网关为主  | IM 网关为主  | 终端 CLI / TUI（+ 扩展桥接 IM） |
| 1h1g 表现 | 频繁 OOM         | 极省         | 极省         | 稳定                            |

最终没有选 ZeroClaw，原因很实际：它的文档充斥着 *AI Assistant Infrastructure*、*3-layer memory*、*ACP Gateway* 这类架构黑话，翻完 README 依然不知道它到底怎么跑起来。GitHub issue 里也有人抱怨安装说明被从 README 里移除了。

Pi 的吸引力在于**极简内核**：核心只带 4 个工具（`read` / `write` / `edit` / `bash`），系统提示词压缩在 1000 token 以内，高级能力全部靠插件按需加载；记忆用纯 Markdown 文本（`~/.pi/agent/memory/MEMORY.md`），没有后台数据库和向量检索——这对单核 CPU 极其友好。

再加上作者 Mario Zechner（libGDX 作者）维护了官方 Telegram 桥接扩展 `badlogic/pi-telegram`，正好覆盖"手机上遥控"这个需求，于是定了：

> **Pi Coding Agent + pi-telegram + systemd**

## 一、系统与虚拟化参数

### 系统：Debian 12 64 位

- 纯净安装后开机常驻内存约 **60~90MB**；相比之下 Ubuntu 带 `snapd` 等默认服务要 200~300MB。
- Debian 12 自带 glibc 2.36+，官方支持 Node.js 22（Pi 的硬性要求）。Debian 9/10、CentOS 7 会因为底层库过老而直接装不上 Node 22。
- 面板上「推荐配置：至少 2GB 内存」是模板化提示，可以忽略。

### 虚拟化参数（KVM 面板）

| 项目         | 选择              | 原因                                                         |
| :----------- | :---------------- | :----------------------------------------------------------- |
| Boot Order   | Hard Disk → CDROM | 系统已装在虚拟硬盘，优先硬盘启动                             |
| Network Card | **Virtio**        | 半虚拟化，零硬件模拟开销；Realtek 8139 / Intel PRO/1000 是全模拟网卡，单核机上 CPU 占用高、跑不满带宽 |
| Disk Driver  | **virtio**        | 映射为 `/dev/vda`。选 `ide` 会让读写变慢，跑 npm 时 `%iowait` 直接飙满卡死 |
| Clock        | **UTC**           | Linux 服务器标准。时钟错乱会导致 TLS 证书校验失败，API 调用直接报错 |

### 防火墙

只需放行 **22 (SSH)** 和 **ICMP (Ping)**。

Telegram Bot 用的是**服务器主动出站的长轮询（long polling）**，不需要开放任何入站端口。ICMP 建议保留——它只是网络层协议，不构成提权风险，但出故障时一句 `ping` 就能判断是机器宕机还是 SSH 客户端的问题。

## 二、系统初始化

```bash
# 1. 更新索引并安装基础工具
apt update && apt install -y curl git tmux ca-certificates

# 2. 时区设为东八区（日志更直观；硬件时钟仍保持 UTC）
timedatectl set-timezone Asia/Shanghai

# 3. 创建 2GB Swap（1G 内存的保命设置）
fallocate -l 2G /swapfile || dd if=/dev/zero of=/swapfile bs=1M count=2048
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab

free -m   # 确认 Swap 约为 2048M
```

> Swap 不是可选项。Agent 偶尔拉一个长网页、跑一次 `npm`，内存都会瞬间冲高；没有 Swap 兜底就是直接 OOM。

## 三、安装 Node.js 22 与 Pi

```bash
# Node.js 22 LTS
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt install -y nodejs
node -v      # 必须 >= v22

# 全局安装 Pi Coding Agent
npm install -g --ignore-scripts @earendil-works/pi-coding-agent

# 安装官方 Telegram 桥接扩展
pi install git:github.com/badlogic/pi-telegram
```

**建立独立工作目录**——这一步别省。Coding Agent 会把「启动它的目录」当作工作区根目录，直接在 `/root` 下启动，以后生成的脚本、仓库、临时文件会全堆在系统目录里。

```bash
mkdir -p ~/workspace && cd ~/workspace
pi
```

## 四、接入自建中转站

如果你用的是官方 Anthropic / OpenAI Key，直接在环境变量里配 `ANTHROPIC_API_KEY` / `OPENAI_API_KEY` 即可。但用**第三方中转站（OneAPI / NewAPI / 聚合平台）**时，`/login` 菜单里只有官方内置提供商，**没有"添加 OpenAI 兼容端点"这一项**，必须走配置文件。

Pi 的自定义模型全部存在 `~/.pi/agent/models.json`：

```json
{
  "providers": {
    "my-proxy": {
      "baseUrl": "https://api.example.com",
      "api": "anthropic-messages",
      "apiKey": "sk-xxxx",
      "models": [
        {
          "id": "deepseek-v4.1-flash",
          "name": "DeepSeek V4.1 Flash",
          "input": ["text", "image"],
          "contextWindow": 1000000,
          "maxTokens": 8192
        },
        {
          "id": "gpt-5.6-luna",
          "name": "GPT-5.6 Luna",
          "input": ["text", "image"],
          "contextWindow": 1000000,
          "maxTokens": 8192
        },
        {
          "id": "gpt-6.1-sol",
          "name": "GPT-6.1 Sol",
          "input": ["text", "image"],
          "contextWindow": 1000000,
          "maxTokens": 8192
        }
      ]
    }
  }
}
```

写入文件（注意 `EOF` 必须独占一行，粘贴时别和上一条命令粘在一起）：

```bash
mkdir -p ~/.pi/agent
cat << 'EOF' > ~/.pi/agent/models.json
{ ... 粘贴上面的 JSON ... }
EOF
```

三个必须注意的点：

1. **`baseUrl` 不要带 `/v1`**。选择 `"api": "anthropic-messages"` 时，Pi 会自动拼接 `/v1/messages`；再手动加 `/v1` 就变成 `/v1/v1/messages`，直接 404。
2. **`id` 必须和中转站后台的模型 ID 完全一致**，通常是小写连字符格式（`deepseek-v4.1-flash`），写成 `DeepSeek V4.1 Flash` 会调不通。**以你自己的中转站后台显示的名称为准。**
3. 密钥不要直接写进文件后随手 `git commit`，可以用 `"$MY_KEY"` 形式读环境变量。

配好后进入 `pi` 执行 `/model`，就能看到并选择中转站下的模型。

## 五、Telegram Bot 与单用户配对

1. 在 Telegram 找 `@BotFather`，`/newbot` 创建机器人，拿到 `Bot Token`（形如 `7812345678:AAH...`）。
2. 在 `~/workspace` 下启动 `pi`，依次输入：

```text
/telegram-setup      # 粘贴 Bot Token
/telegram-connect    # 开始长轮询
```

3. 手机打开你刚建的 Bot，发送 `/start`。

`pi-telegram` 采用**首位私聊用户绑定机制**：第一个发消息的用户 ID 会被永久锁定，之后其他任何人发消息都会被忽略。因为 Agent 持有 `bash` 执行权限，这层单用户锁定是必须的安全边界——**务必在部署完成后立刻完成配对，不要让它长时间处于"谁先说话谁绑定"的状态**。

## 六、用 systemd 做常驻守护

`tmux` 手动挂后台能用，但有两个问题：命令繁琐，且**重启后不会自动起来**。

Pi 是交互式 TUI，不能像普通 daemon 一样直接丢后台。可行做法是**用 systemd 管理一个脱离的 tmux 会话**，再通过 `tmux send-keys` 自动注入启动指令。下面这个 service 文件把全部逻辑收在一个文件里，不额外生成脚本：

`/etc/systemd/system/pi-agent.service`

```ini
[Unit]
Description=Pi Telegram Agent
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
RemainAfterExit=yes
User=root
WorkingDirectory=/root/workspace
Environment=HOME=/root
Environment=PATH=/usr/local/bin:/usr/bin:/bin
ExecStartPre=-/usr/bin/tmux kill-session -t agent
ExecStart=/usr/bin/tmux new-session -d -s agent -c /root/workspace
ExecStart=/bin/sleep 1
ExecStart=/usr/bin/tmux send-keys -t agent "pi" C-m
ExecStart=/bin/sleep 2
ExecStart=/usr/bin/tmux send-keys -t agent "/telegram-connect" C-m
ExecStop=-/usr/bin/tmux kill-session -t agent

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
systemctl enable pi-agent
systemctl restart pi-agent
systemctl status pi-agent -l --no-pager
```

### 这个文件为什么这么写

| 设计                                   | 解决的问题                                                   |
| :------------------------------------- | :----------------------------------------------------------- |
| `Type=oneshot` + `RemainAfterExit=yes` | 拉起会话并注入指令是一次性任务；`simple` 类型会把命令结束当作服务退出，进而把 tmux 当成孤儿进程清掉 |
| 多行 `ExecStart` 顺序执行              | 利用 oneshot 原生支持多条 `ExecStart` 的特性，**彻底避开 systemd 对单双引号嵌套的解析问题** |
| `ExecStartPre` 前的 `-`                | 表示「尽力执行，失败也放行」。首次启动没有旧会话时不会中断启动 |
| 显式 `Environment=HOME/PATH`           | systemd 不加载 `~/.bashrc`，缺这两项会找不到 `~/.pi/agent/` 下的配置文件 |
| `send-keys ... C-m`                    | `C-m` 即回车，把人工敲命令的过程自动化                       |

启动成功后状态是 `Active: active (exited)`，`Memory` 一项显示整个 cgroup（含 tmux、bash、pi）的合计占用。

## 七、踩坑记录

实测踩到的坑，按出现顺序记录：

| 现象                                                         | 原因                                                         | 解法                                        |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :------------------------------------------ |
| `WantedBy=multi-user.targetEOF`、`daemon-reloadoad`          | 粘贴时命令与 heredoc 结束符粘连                              | 逐段粘贴，`EOF` 必须独占一行                |
| `Job for pi-agent.service failed`（无具体原因）              | `ExecStart` 里用 `bash -c '...'` 多层引号嵌套，被 systemd 解析器吞掉 | 改为多行 `ExecStart`，不再用 `bash -c`      |
| 服务找不到配置文件                                           | systemd 环境下没有 `HOME`                                    | 加 `Environment=HOME=/root`                 |
| `status=203/EXEC`、`Failed to locate executable /usr/bin/tmux` | 系统根本没装 tmux                                            | `apt install -y tmux`                       |
| `error connecting to /tmp/tmux-0/default (No such file or directory)` | `ExecStartPre` 去杀一个不存在的会话                          | **无害提示**，前面的 `-` 已让它放行，可忽略 |
| 模型调不通 / 404                                             | `baseUrl` 多了 `/v1`，或 `id` 大小写与中转站不一致           | 只填根域名；`id` 用后台真实名称             |
| `/login` 里找不到"添加 OpenAI 兼容端点"                      | 该菜单只列官方提供商                                         | 改用 `~/.pi/agent/models.json`              |
| 生成的文件堆在 `/root`                                       | 在 `/root` 直接启动了 `pi`                                   | 固定从 `~/workspace` 启动                   |

## 八、实测资源占用

systemd 报告整个服务 cgroup 的内存占用：

```text
Active: active (exited)
Memory: 57.5M
CGroup:
  ├─ /usr/bin/tmux new-session -d -s agent -c /root/workspace
  ├─ -bash
  └─ pi
```

系统整体（含 Debian 底噪）通常落在 200~350MB 区间，1G 机器上还留有约 700MB 缓冲。相比 Hermes 动辄 500MB+ 的常驻占用，这是能把小机长期跑下去的关键。

## 九、日常使用

### 服务管理

| 操作                     | 命令                                                |
| :----------------------- | :-------------------------------------------------- |
| 启动（并自动连上 TG）    | `systemctl start pi-agent`                          |
| 停止（并清理 tmux 会话） | `systemctl stop pi-agent`                           |
| 重启（改了配置后）       | `systemctl restart pi-agent`                        |
| 查看状态与内存           | `systemctl status pi-agent -l --no-pager`           |
| 切入实时终端             | `tmux a -t agent`（退出按 `Ctrl+B` 再按 `D`）       |
| 查看启动日志             | `journalctl -xeu pi-agent.service --no-pager -n 50` |

### Telegram 里的实用指令

- **文件双向传输**：直接把配置文件、日志、截图发给 Bot，它会下载到工作区分析；也可以让它"把刚才写的备份脚本发给我"，作为附件回传。
- **`/stop`**：中断正在执行的长任务或死循环。
- **`/compact`**：压缩历史上下文，省 token、加快响应。

典型用法：

> 「查一下系统内存、磁盘占用，顺便看最近的 SSH 登录失败日志。」
>
> 「写一个每 10 分钟检测端口、挂了自动拉起的脚本，放当前目录，加执行权限并跑一次验证。」

### 需要备份的文件

```text
~/.pi/agent/models.json      # 中转站与模型配置
~/.pi/agent/telegram.json    # Telegram Bot 绑定信息
/etc/systemd/system/pi-agent.service
```

## 十、复盘

**关于选型**：低配机上，"框架完整度"和"可运行性"是矛盾的。带本地向量库、多层记忆、内置网关的 Agent 在 1G 内存上是奢侈品；把记忆降级成 Markdown、把工具降到 4 个原子操作，反而换来了 10 倍级的内存差距和更可控的行为。

**关于底座**：Debian 12 + VirtIO 是低配 KVM 的标准答案。省下的每一分 CPU 和 IO，都会在跑 npm、编译依赖、做长上下文推理时还给你。

**关于常驻**：交互式 TUI 程序上生产，`systemd + tmux + send-keys` 是一个通用、不优雅但极其可靠的模式。多行 `ExecStart` 比任何 `bash -c` 拼串都好维护。

**关于边界**：这个方案给你的是一个**单人专用**的终端代理。单用户配对是安全底线，别为了省事把它开成多用户服务——它持有 root 的 `bash` 权限。

## 参考资料

- [NousResearch/hermes-agent](https://github.com/nousresearch/hermes-agent) — 自我进化的重型 Agent 参考
- [earendil-works/pi](https://github.com/earendil-works/pi) — Pi Agent 工具包主仓库
- [@earendil-works/pi-coding-agent (npm)](https://www.npmjs.com/package/@earendil-works/pi-coding-agent)
- [badlogic/pi-telegram](https://github.com/badlogic/pi-telegram) — 作者官方 Telegram DM 桥接扩展
- [Qusic/pi-telegram](https://github.com/Qusic/pi-telegram) — 社区另一实现
- [zeroclaw-labs/zeroclaw](https://github.com/zeroclaw-labs/zeroclaw)、[sipeed/picoclaw](https://github.com/sipeed/picoclaw) — Rust / Go 备选方案
- [pi-memory 包说明](https://pi.dev/packages/pi-memory) — 纯 Markdown 记忆机制