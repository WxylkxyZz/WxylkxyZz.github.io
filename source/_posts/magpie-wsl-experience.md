---
title: "127.0.0.1 到底是谁的？Claude Desktop 连 WSL2 本地网关的九层排障记"
date: 2026-11-17 14:28:27
categories:
  - "随笔"
tags:
  - "WSL"
  - "Claude Desktop"
  - "Magpie"
aging: true
aging_days: 30
---

> 一次看似"网络不通"的故障，最后挖出九个彼此独立的坑：从 NAT 语义、回环转发递归，到 systemd 沙箱阻断进程互操作、keep-alive 占满连接槽位。没有一层是靠猜的。

**环境**：Windows 10 IoT Enterprise LTSC 21H2 (10.0.19044.7846) + WSL2 2.7.14.0 / Ubuntu-24.04 + Claude Desktop 自定义网关模式 + Magpie 0.1.4342
**最后更新**：2026-11-17

<!--more-->

## 事情的起点

Claude Desktop 的 Code 标签页可以连远程会话。我想让它跑在本机的 WSL2 Ubuntu 里，推理则走同样在本机的 Magpie 网关——听起来只要两边能通就行。

结果界面只给了一句：

> Check your internet connection, VPN, or proxy and try again.

而 Magpie 的日志里，一条请求都没有。

这篇文章记录我是怎么把这句话拆成九层根因、以及最后那套稳定跑起来的方案的。文中不涉及任何密钥或凭据。

---

## 最终跑起来的链路长什么样

让 **Claude Desktop 的 Code 标签页** 能创建并正常使用 **WSL2 Ubuntu-24.04 会话**，而推理走本机 Windows 上的 **Magpie 网关**。

最终生效的链路：

```text
Claude Desktop（Windows 宿主）
  └─ SSH 启动 Ubuntu-24.04 内的 ccd-cli
       └─ CLI 访问 http://127.0.0.1:3425（WSL 自身回环）
            └─ claude-ipv6-bridge.service（WSL systemd 常驻）
                 └─ 经 WSL interop 启动 Windows python.exe 工作进程
                      └─ 连接 Windows [::1]:3425
                           └─ Magpie 网关
                                └─ 上游 CPA provider
```

关键点只有一句：**WSL 侧监听 `127.0.0.1:3425` 冒充网关，Windows 侧必须走 IPv6 回环 `[::1]` 出去。**

---

## 为什么必须加一层桥：WSL 里的 127.0.0.1 不是你的 Windows

Desktop 给远端 CLI 注入的网关地址是**写死的**：

```text
http://127.0.0.1:3425
```

来源：`main.log` 的 `[custom-3p] inference apiHost=http://127.0.0.1:3425`，以及打包 JS 里用 `apiHost` 拼 `ANTHROPIC_BASE_URL`。

而 WSL2 默认 NAT 网络下：

- WSL 里的 `127.0.0.1` 是 **WSL 自己的回环**，不是 Windows 的；
- `localhostForwarding` 的方向是 **Windows → WSL**，反方向没有转发。

于是 CLI 连 `127.0.0.1:3425` 直接 `ECONNREFUSED`，请求根本到不了 Windows 上的 Magpie——这就是最初界面报 "Check your internet connection, VPN, or proxy"、而 Magpie 日志里一条请求都没有的原因。

### 为什么不能直接改成宿主机地址

把 `inferenceGatewayBaseUrl` 改成 `http://172.20.160.1:3425`（WSL 的 Windows 网关地址）会被 Desktop 拒绝：

```text
Invalid custom3p managed config: inferenceGatewayBaseUrl
… must use https (or http on loopback)
```

即 Desktop 只接受 **回环地址 + HTTP**，或 **任意地址 + HTTPS**。明文 HTTP 打 NAT 地址不合法。

### 为什么一定要 IPv6

最早的补法是在 WSL 里监听 `127.0.0.1:3425` 再转到 `172.20.160.1:3425`，结果出现两个问题：

1. 走 NAT 入口会强制校验网关 key，回环入口不校验，行为不一致；
2. 改成 Windows 管道转发后出现 **6612 次连接放大**，且永远收不到首字节。

递归机制（已用独立实验证实：WSL 监听临时端口后，从 Windows 连 `127.0.0.1:同端口` 能打到 WSL 内的监听器）：

```text
WSL 127.0.0.1:3425
  → Windows 工作进程
  → Windows 127.0.0.1:3425
  → 被 localhostForwarding 送回 WSL 3425
  → 无限递归
```

Magpie 实际监听的是 `::3425`（IPv4/IPv6 双栈），而 `localhostForwarding` 只管 IPv4。所以 Windows 侧改用 `[::1]:3425` 就能**绕开递归**，且仍然落在回环入口上。

---

## 九个坑，一个一个踩

| # | 现象 | 根因 | 修法 |
|---|---|---|---|
| 1 | Magpie 收不到任何请求 | WSL2 NAT 下 `127.0.0.1` 是 WSL 自己的回环 | 必须加桥 |
| 2 | 改 NAT 地址被拒 | Desktop 只允许 https 或 http-on-loopback | 保留 `127.0.0.1` |
| 3 | 数千连接、永不返回 | IPv4 同端口转发 + localhostForwarding 递归 | 上游改 `[::1]` |
| 4 | 通了但立刻 `unrecognized_model` | 会话里存的模型 ID 是 `mythos-magpie-*`，网关只提供 `cpa/*` | 会话模型切到 `/v1/models` 实际返回的 ID |
| 5 | 短消息能过，正常消息失败 | 初版桥空闲 10s / 总时长 120s，流式推理被掐断 | 空闲 300s、单连接 900s |
| 6 | 服务 active、端口在听，但恒定 0 字节 | systemd 加固（`NoNewPrivileges`/`ProtectSystem`/`ProtectHome`/`PrivateTmp`）阻断 `/run/WSL/*_interop` | 移除这四项加固 |
| 7 | 跑一会儿就失败，Magpie 报端口被占用 | `wslrelay.exe` 抢占 Windows 3425 → Magpie 退到 3426 → WSL 无 3426 监听 → `errno=10061` | `.wslconfig` 设 `localhostForwarding=false` |
| 8 | 间歇 reset、0 字节 | keep-alive 长连接占满 8 个槽位，`connections_rejected` 飙升 | 上限提到 64，按**当前活动连接数**判定 |
| 9 | 运行中断流 | 上游 CPA 返回 HTTP 200 内嵌错误（见下文"那些看起来像本地故障的报错"） | 非本地问题，重试或换模型 |

第 7 层值得单独记住，它会自我延续：

```text
WSL 桥监听 P
  → localhostForwarding 生成 wslrelay.exe 占用 Windows P
  → Magpie 绑定 P 失败，退到 P+1
  → Desktop 跟随到 P+1
  → WSL 桥也要听 P+1
  → 再被 wslrelay 抢占 ……
```

所以 `localhostForwarding=false` 不是可选优化，是**前置条件**。

---

## 最终的配置清单

### 1. `C:\Users\admin\.wslconfig`

```ini
[wsl2]
localhostForwarding=false
```

代价：从 Windows 访问 WSL 内服务不能再直接用 `localhost:端口`，要改用 WSL 的 IP。

### 2. `C:\Users\admin\.config\magpie\settings.json`

与本方案相关的字段（只列非敏感的）：

```json
{
  "port": 3425,
  "vision": "cpa/gpt-5.6-luna",
  "pickedModels": {
    "claude-desktop": ["cpa/deepseek-v4.1-flash", "cpa/gpt-5.6-luna", "cpa/gpt-6.1-sol"]
  },
  "modelCompacts": { "cpa/*": 600000 },
  "agentsHere": ["claude@wsl:Ubuntu-24.04"]
}
```

该文件含凭据字段（如 `lanKey`），**不要整份外传或截图**。

### 3. `/etc/systemd/system/claude-ipv6-bridge.service`

```ini
[Unit]
Description=Claude WSL IPv6 loopback bridge to Windows Magpie
After=local-fs.target network-online.target
Wants=network-online.target

[Service]
Type=simple
User=ubuntu
Environment=PYTHONUNBUFFERED=1
Environment=WSL_INTEROP=/run/WSL/1_interop
Environment=WSL_DISTRO_NAME=Ubuntu-24.04
Environment=PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ExecStart=/usr/bin/python3 /mnt/d/Claude/SNAP/tools/wsl-windows-pipe-ipv6-service.py
Restart=always
RestartSec=5
KillSignal=SIGTERM
TimeoutStopSec=45

[Install]
WantedBy=multi-user.target
```

**不要**加回 `NoNewPrivileges` / `ProtectSystem` / `ProtectHome` / `PrivateTmp`——会让 interop 失效，表现为服务正常但请求全是 0 字节。

### 4. 桥的关键参数

```text
监听：127.0.0.1:<从 Magpie settings.json 读 port，当前 3425>
上游：Windows [::1]:<自动探测 3425/3426/3427/3430>
最大并发：64（按当前活动连接数计，不是累计）
I/O 空闲超时：300 秒
单连接上限：900 秒
单方向上限：16 MiB
重启：Restart=always / RestartSec=5
不记录请求内容和密钥
```

### 5. `/etc/wsl.conf`

```ini
[boot]
systemd=true
```

没有它，服务根本不会开机启动。

---

## 怎么验证它真的通了

三层都要单独验，否则分不清是哪一层坏了。

**第一层：Windows 上 Magpie 活着**

```bash
netstat -ano | grep -E "LISTENING" | grep 3425
```

应看到 PID 同时监听 `0.0.0.0:3425` 和 `[::]:3425`，且**没有 `wslrelay.exe` 占着这个端口**。

**第二层：WSL 侧桥在听**

```bash
MSYS_NO_PATHCONV=1 wsl.exe -d Ubuntu-24.04 --user root -- ss -ltn
```

应有 `LISTEN 127.0.0.1:3425`。

**第三层：端到端**

```bash
MSYS_NO_PATHCONV=1 wsl.exe -d Ubuntu-24.04 -- bash -lc "curl -sS -m 20 -o /dev/null -w 'code=%{http_code} bytes=%{size_download}\n' http://127.0.0.1:3425/v1/models"
```

健康时形如 `code=200 bytes=2657`。**0 字节 = interop 被阻断**（查第 6 层坑），不是网络不通。

---

## 那些看起来像本地故障、其实不是的报错

这两类错误都出现在 **HTTP 200** 之后，属于上游服务在流里内嵌的错误，改桥、改 Magpie、换密钥都没用。

### 1. `Complexity '<level>' is not supported for this image model`

```text
2026/11/05 15:27:19 gateway: claude-desktop's call to cpa/gpt-5.6-luna on cpa failed: 200 after 21.577s: Complexity 'high' is not supported for this image model.
2026/11/05 16:08:04 gateway: claude-desktop's call to cpa/gpt-6.1-sol  on cpa failed: 200 after 25.597s: Complexity 'medium' is not supported for this image model.
```

判定依据：

- `magpie.exe` 里 `b"Complexity"` 出现次数为 **0**，`b"is not supported for this image model"` 也为 0 → 不是 Magpie 生成的；
- 状态码是 200，说明是上游返回后夹带在正文里的错误；
- 本地对 `127.0.0.1:3425` 逐个试探了 `reasoning_effort` 低/中/高、`reasoning.effort`、`metadata.complexity`、顶层 `complexity`、带图/不带图、`thinking.budget_tokens` 各档位，**全部 200 正常**，无法在本地稳定复现。

推测：上游把某些请求路由到它内部的图像模型通道，而那条通道只接受固定的 complexity 级别。属于 CPA 侧行为。

处理：重试；或该会话改用未出现过此错误的 `cpa/deepseek-v4.1-flash`。

### 2. `CPA: connection lost mid-reply (unexpected EOF)`

```text
2026/11/17 09:29:46 gateway: claude-desktop's call to cpa/gpt-6.1-sol on cpa failed: 200 after 22.412s: CPA: connection lost mid-reply (unexpected EOF)
```

上游长连接被切断。长回答、重推理时更容易出现。本地桥已把超时放宽到 300s/900s，所以**不是**我们这侧掐的。

---

## 装好之后怎么维护

### 看服务状态

```bash
MSYS_NO_PATHCONV=1 wsl.exe -d Ubuntu-24.04 --user root -- systemctl status claude-ipv6-bridge.service
```

### 看日志

```bash
MSYS_NO_PATHCONV=1 wsl.exe -d Ubuntu-24.04 --user root -- journalctl -u claude-ipv6-bridge.service -n 50 --no-pager
```

日志里认这几个标记：

```text
WSL_IPV6_SERVICE_READY    启动成功，已打印监听与上游
WSL_IPV6_SERVICE_STOPPED  退出，附带 accepted / rejected 等计数
BRIDGE_WORKER_START_ERROR interop 起不来，附 errno 与 interop 环境
```

### 重启 / 停用

```bash
MSYS_NO_PATHCONV=1 wsl.exe -d Ubuntu-24.04 --user root -- systemctl restart claude-ipv6-bridge.service
```

```bash
MSYS_NO_PATHCONV=1 wsl.exe -d Ubuntu-24.04 --user root -- systemctl disable --now claude-ipv6-bridge.service
```

### 切换 WSL 会话模型

在该会话自己的模型选择器里选，或会话内执行：

```text
/model cpa/gpt-6.1-sol
```

**只能用 `/v1/models` 实际返回的 ID。** `mythos-magpie-*` 是旧的本地别名，远端 CLI 2.1.317 会直接拒绝。

### Magpie 换端口后

桥只在**启动时**读一次 `settings.json` 的 `port`。Magpie 换了端口（比如又被抢占），必须重启桥。

---

## 排障速查：症状对原因

| 现象 | 优先检查 |
|---|---|
| Magpie 完全无请求 | 桥是否 active；WSL 内 `ss -ltn` 有无 `127.0.0.1:3425` |
| 桥 active、端口在听，但 0 字节 | 单元文件是否残留 systemd 加固；`/mnt/d/Software/Python/python.exe` 是否存在 |
| 出现数千连接 | 是否误回退到 IPv4 同端口转发（必须 `[::1]`） |
| Magpie 报端口被占用 | 有无 `wslrelay.exe` 占端口；`.wslconfig` 的 `localhostForwarding` 是否被改回 true |
| 跑一阵后新请求被 reset | journal 里 `connections_rejected` 是否增长；上限应为 64 |
| `errno=10061` | Magpie 实际端口 ≠ 桥的上游端口；看 `magpie/settings.json` 的 `port`，重启桥 |
| `unrecognized_model` | 会话模型是否在 `/v1/models` 列表里 |
| `ECONNRESET` / 0 字节 | 桥被超时截断，或 interop 被 sandbox 阻断 |
| 长回答被截断 | I/O 空闲超时（现为 300s）或上游 EOF |
| `must use https (or http on loopback)` | 是否把网关改成了非回环明文 HTTP |
| `Complexity ... image model` | 上游 CPA 行为，重试或换模型，别改本地配置 |

---

## 这套方案仍然脆弱的地方

1. **未做冷启动全链路验证** —— 没有执行 `wsl --shutdown` 后的完整冷启测试，开机自启目前只有 `systemctl is-enabled` 层面的证据。
2. **`/etc/wsl.conf` 依赖** —— `[boot] systemd=true` 一旦被改，服务不会启动。
3. **Windows Python 路径硬编码** —— 依赖 `/mnt/d/Software/Python/python.exe`。卸载、换盘、升级路径变化都会让桥失效。
4. **Magpie 必须保留 IPv6 回环监听** —— 当前是 `[::]:3425` 双栈。若改成只监听 `127.0.0.1`，`[::1]` 连不上，整套方案要重新设计。
5. **模型 ID 会变** —— Magpie 换 provider 或模型后，Desktop 里保存的旧 ID 会再次触发 `unrecognized_model`。排查先看 `/v1/models`。
6. **`localhostForwarding=false` 是全局行为变更** —— 影响所有 Windows→WSL 的 localhost 访问。
7. **上游 CPA 稳定性不在掌控内** —— `Complexity`、`connection lost mid-reply` 都可能随时出现。

---

## 比配置更值得记的七条

### 1. 不要在应用运行时改它的配置文件

在 Desktop 运行期间直接编辑 `claude_desktop_config.json` 改会话模型，结果 Desktop 在 1 分钟后自己重写覆盖，期间宿主会话短暂断联。

**规矩**：改 Desktop 配置要么在界面里操作，要么先完全退出 Desktop。

### 2. 不要凭一个 401 就断定"密钥错了"

曾因临时转发拿到 401，倾向判断"Desktop 保存的密钥有问题"。后来对照证明两个入口策略不同：

| 入口 | 不带鉴权 | 带无效 Bearer |
|---|---|---|
| `127.0.0.1:3425`（回环） | 200 | 200 |
| `172.20.160.1:3425`（NAT） | 401 | 401 |

那个 401 是**入口策略差异**，与密钥无关。用户只有一把密钥且一直在用，从未要求更换。

### 3. 0 字节响应优先怀疑 interop，不是网络

服务 `active`、端口在听、却每个请求 0 字节 —— 症状很像网络，实际是 systemd sandbox 挡住了 `/run/WSL/*_interop`。看到 0 字节先查单元文件加固项。

### 4. 时长的 errno 是 None

`PIPE_DOWNLOAD_ERROR errno=None` 意味着不是真实 socket 错误，而是**本地超时对象**。看到 `errno=None` 就往超时方向查，别去查网络栈。

### 5. 每层都要能单独证伪

最终这套方案有 9 层坑，能走出来靠的是每层都做**独立对照实验**（例如用临时端口证明 localhostForwarding 方向、用无敏感标记证明子进程管道可用），而不是靠猜。中途有一次我把"同端口递归"写成了"已排除"，后来靠受控实验才发现是错的，已在报告里更正。

### 6. 分清"本地问题"和"上游问题"

`Complexity` 报错排查花了不少时间，最后靠**在二进制里搜字符串为 0** 才确认它根本不在 Magpie 里。以后再遇到奇怪报错，第一步先搜二进制、看它到底是谁产生的。

### 7. 代理工具（WSL 命令）的路径会被 MSYS 改写

Git Bash 下 `/mnt/d/...` 会被改写成 `D:/Software/Git/mnt/...`。所有跨 `wsl.exe` 传路径的命令前加 `MSYS_NO_PATHCONV=1`。

---

## 写在最后

如果只用一句话概括这次故障：

> Desktop 把 `http://127.0.0.1:3425` 写死进 WSL 的 CLI，而 WSL2 NAT 下这个地址只指向 WSL 自己。

剩下的八个坑，都是打通之后才逐个浮出水面的：模型 ID、超时、systemd 沙箱、端口抢占、连接上限。它们的共同点是——**上一层修好了，下一层才看得见**。

这也解释了为什么"直接问怎么解决"很难得到答案：这类问题不是单一 bug，而是一条依赖链。每修一层，报错就换一张脸。

真正让排查效率提上来的，只有两件事：

1. **每层都做能独立证伪的对照实验**。比如用临时端口证明 `localhostForwarding` 的方向，而不是靠读文档推断。
2. **先确认报错是谁产生的**。`Complexity` 那个错查了很久，最后靠搜二进制字符串为 0 才确认它压根不在本地——早做这一步能省掉大半天。

如果你也在做类似的事（Desktop 远程会话 + 本地网关 + WSL），希望这九层能帮你少走几层。
