# Keep Throb — Hexo 博客源码重建

从已部署的静态站（https://wxylkxyzz.github.io/）反向恢复的 Hexo 源码。
原 Hexo 源码已丢失，本目录是重建后的完整工程，可直接生成博客、写新文章。

## 快速开始

```bash
cd ~/workspace/blog/hexo-source
npm install          # 首次需要；node_modules 已装好可跳过
npx hexo clean && npx hexo generate   # 生成到 public/
npx hexo server      # 本地预览 http://localhost:4000
```

发布（暂未配置自动部署，需要时再配）：
- 生成后的 `public/` 就是要发布到 `WxylkxyZz/WxylkxyZz.github.io` 仓库 `master` 分支的内容；
- 可用 `hexo-deployer-git`（`_config.yml` 里配 `deploy`）或直接把 `public/` 推送到该仓库。
- 注意：不要把本源码目录本身推到 `WxylkxyZz.github.io` 仓库（那是纯静态站仓库）。

## 版本（与原站一致，不升级）

- Hexo 6.3.0，主题 hexo-theme-keep 4.1.3
- hexo-renderer-marked 6.0.0（marked 4.3.0）、hexo-generator-search 2.4.3、
  hexo-wordcount 6.0.1、hexo-filter-mathjax 0.11.1
- Node v24.22.1 / npm 10.9.4（本机环境）

## 已恢复的文章（13 篇，全部在 source/_posts/）

| 文章 | 日期 | 分类 | 标签 |
|---|---|---|---|
| 常用Git命令清单 | 2022-05-18 | 随笔 | Git |
| Hello World | 2022-07-14 | 随笔 | 主题 |
| 问题小记（置顶） | 2023-01-18 | 随笔 | 问题 |
| Learn Regex | 2023-04-09 | 随笔 | Regex |
| Vps搭建系列 | 2023-04-13 | 随笔 | VPS |
| Encrypt and Decode | 2023-04-27 | 心血来潮 | Python、加密解密 |
| Python GUI之Tkinter | 2023-04-27 | 随笔 | Hexo、Keep |
| Asyncio | 2023-12-30 | Python | Asyncio |
| BeautifulSoup | 2023-12-30 | Python | BeautifulSoup |
| Learn Python | 2023-12-30 | Python | Python |
| Requests | 2023-12-30 | Python | requests |
| Trap | 2023-12-30 | Python | Python |
| Xpath | 2023-12-30 | Python | Xpath |

另有 About 页、Links 页（友链）、自定义 css/js（`source/css/custom-1.css`、
`source/js/custom-1.js`，从部署文件原样恢复）。

## 恢复方式

提取脚本：`~/workspace/blog/extract_posts.py`（已从 /tmp 复制一份到这里备份），
配置快照：`~/workspace/blog/theme_config.json`（从部署 HTML 内嵌的
`KEEP.theme_config` JSON 导出）。脚本从部署站 HTML 反推 Markdown：
代码块（`<figure class="highlight">`）、表格（手写 Markdown 表并转义 `|`）、
`<!--more-->` 摘要分隔、`aging: true / aging_days: 30`（每篇文章都有过期提醒）、
置顶（`sticky: 1`，仅《问题小记》）。

## 与原站对比（`hexo generate` 生成 103 个文件）

逐项核对过，全部一致：
- 13 篇文章的代码块数量、代码行数、中文字符数、表格（行数与内容）与原站完全相同
- 首页两页的文章顺序、置顶徽章、归档 / 分类 / 标签页文件集合与原站相同
- 9/13 篇文章的可见文本（空白归一化后）与原站逐字相同

## 已知差异（均已确认原因，不影响阅读）

1. **页脚总字数 43.2k vs 原站 41.4k**。字数统计把代码里的 `"`/`'` 计为
   `&quot;`/`&#x27;` 实体碎片当单词。原站用 highlight.js 10.0.0（引号不转义），
   现行 Hexo 6.3.0 要求 hexo-util 2.7.0 + highlight.js 11（转义引号），
   降级 hexo-util 会导致 Hexo 无法启动（已实测并回滚）。渲染效果完全相同。
2. **MathJax 的 CSS 在 44 个页面 vs 原站 15 个页面**。hexo-filter-mathjax
   0.11.1 会往每个页面的 `<head>` 注入一段 `<style>`；原站版本只注入文章页和
   首页。CSS 本身不可见，且没有任何一篇文章真正用到数学公式。
3. **9/162 个标题锚点 ID 不同**：含 `/` 的标题，原站是 `添加-删除文件`，
   现为 `添加-x2F-删除文件`（marked 4 的 slug 规则）。站内目录链接仍能正常工作。
4. **页脚年份 2026 vs 原站 2025**：构建日期导致，重新部署后自然变成当年。
5. **站点 URL**：`_config.yml` 里填的是真实地址 `https://wxylkxyzz.github.io`，
   原站配置疑似留空（`example.com`），这里是有意修正。
6. **search.json 条目顺序**：现为按日期倒序，原站为按标题字母序（生成器版本差异），
   内容 13 条相同，不影响本地搜索。

## 未能精确恢复的内容

- **原始 Markdown 的排版细节**：从 HTML 只能还原渲染结果，不能还原原作者当初的
  写法（如行内代码的反引号风格、标题层级在个别地方是渲染后推断的）。
- **3 张本地图片已丢失**：`/imgs/01-06.png`、`/imgs/01-07.png`（Requests 一文）、
  `/./file-open-mode.png`（Learn Python 一文）——部署仓库里也没有，线上站同样是
  坏图；引用保留原样，效果与线上一致。
- **个别 Markdown 语法嵌套**：4 篇文章有零星几个字符级差异（如《问题小记》里展示
  代码围栏的 ``` 样本、《Asyncio》里被当成列表/标题解析的 `3.`、`4.`、`###`），
  属于“用 Markdown 写 Markdown”的歧义，内容本身无缺失。
- **一处手工修正**：`Learn-Python.md` 里 `+ : 打开一个文件进行更新` 一行，
  开头的 `+` 必须转义为 `\+`，否则会被解析为无序列表而丢失该字符。
  若重跑提取脚本，需重新做这处修正。
