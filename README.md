# 📚 WeRead Notes Export — 微信读书笔记导出 Skill

[![skills.sh](https://skills.sh/b/dongwei6688/weread-notes-export-skill)](https://skills.sh/dongwei6688/weread-notes-export-skill)
[![GitHub release](https://img.shields.io/github/v/release/dongwei6688/weread-notes-export-skill)](https://github.com/dongwei6688/weread-notes-export-skill/releases)

把微信读书的**划线（书签）**和**想法（评论/批注）**按**章节树**导出为本地结构化 Markdown 文件。支持增量同步、安全文件名、多行内容健壮处理。

## ✨ 亮点

### 按章节树组织，不是简单平铺
从 API 获取书籍的完整章节结构，每条划线自动归入所属章节。一本书的笔记就是一个**有层次的 Markdown 文件**。

### 同名书按书名保存
笔记文件按书名保存（`书名.md`），全平台安全文件名处理。同名不同版本的书（如不同译者）共享同一文件。

### 多行内容健壮处理
多行划线（含署名行）压平为单行、评论内空行自动整理、Unicode 行分隔符（U+2028/U+2029）统一处理——导出文件干净、可被下游解析器逐行正确解析。

### 结构化 JSON 导出（数据分析友好）
加 `--json` 参数，同一次导出同时产出 `.md`（人读）+ `.json`（结构化，保留划线 range、评论原始多行内容、独立想法 abstract、**微信 CDN 封面 URL、网页版阅读器链接**（`web/reader/{urlId}`）等全字段）。**JSON 文件名 = 真实 bookId**（`{bookId}.json`），同名书不再互相覆盖。数据分析直接用 JSON，无需解析 Markdown。

### 安全文件名处理
书名中的特殊字符自动替换为兼容字符，**Windows / Linux / macOS 全平台通用**。

### 条目间自动加分隔线
每条划线之间自动插入 `---` 分隔线，带评论的划线+评论作为一个整体。

### Markdown 格式化
format_notes.py 对导出结果做行间距优化和格式统一

### 增量同步，适合每日 cron
跑一次 `--all` 全量导出后设个 cron 每天增量同步，只导出最近 48 小时内有更新的书。

### 零外部依赖
纯 Python 3 标准库（`urllib` + `json`），无需 `pip install` 任何包。有 Python 就行。

### 跨平台安装（已上架 skills.sh）
```bash
npx skills add https://github.com/dongwei6688/weread-notes-export-skill --skill weread-notes-export
```

---

## 快速开始

### 1. 获取 API Key

打开 https://weread.qq.com/r/weread-skills → 点击**获取 API Key** → 微信扫码登录 → 复制 Key。

或在微信读书 App → **设置** → 底部获取 API Key（扫码或复制 `wrk-xxx`）。

```bash
export WEREAD_API_KEY=wrk-xxxxxxxx
```

### 2. 安装 Skill

确保系统已安装 Node.js（`node --version` 确认，没有去 [nodejs.org](https://nodejs.org) 下载）。

```bash
npx skills add https://github.com/dongwei6688/weread-notes-export-skill --skill weread-notes-export
```

装完后继续下一步导出笔记即可。

### 3. 导出笔记

```bash
# 查看统计
python3 scripts/export_weread_notes.py --stats

# 导出一本书
python3 scripts/export_weread_notes.py --book "原则"

# 导出全部
python3 scripts/export_weread_notes.py --all

# 查看最近更新的书
python3 scripts/export_weread_notes.py --recent

# 同时导出结构化 JSON（数据分析用）
python3 scripts/export_weread_notes.py --book "原则" --json
python3 scripts/export_weread_notes.py --all --json
```

---

## 输出示例

```markdown
# 《原则》读书笔记
作者：瑞·达利欧

## 导言

> 不管我一生中取得了多大的成功，其主要原因都不是我知道多少事情，
> 而是我知道在无知的情况下自己应该怎么做。

---

> 独立思考并决定：（1）你想要什么；（2）事实是什么；
> （3）面对事实，你如何实现自己的愿望……

## 第一部分 我的历程

> 你要问自己要什么，将那些得到了你想要的东西的人作为范例……

---

## 第二部分 生活原则

> 世界上最重要的事情是理解现实如何运行，以及如何应对现实。

💬 这个观点很实用，梦想+现实+决心=成功的生活

---
```

### 输出目录

默认输出到 `~/.weread-notes/`，可通过 `WEREAD_NOTES_DIR` 环境变量自定义。

---

## 命令参考

| 命令 | 说明 |
|------|------|
| `--stats` | 统计：有笔记的书总数、划线数、笔记数、想法数 |
| `--list` | 列出所有有笔记的书及其划线/笔记/想法数量 |
| `--recent` | 查看最近 7 天更新过的书 |
| `--book <书名/ID>` | 按书名或 bookId 导出单本 |
| `--all` | 全量导出所有有笔记的书 |
| `--json` | 与 `--book`/`--all` 组合：同时输出结构化 `.json`（数据分析用） |
| `--json-only` | 仅输出 `.json` 不写 `.md`（每日增量只产 JSON 时用） |

---

## 每日自动同步

```bash
# 添加到 crontab（每天早上 7 点）
# npx skills add 安装:
0 7 * * * cd ~/.agents/skills/weread-notes-export/scripts && python3 daily_sync_weread.py
# 手动安装（替换为实际路径）:
# 0 7 * * * cd /path/to/skill/scripts && python3 daily_sync_weread.py
```

或者直接使用环境变量方式：

```bash
# 每天 7:00 自动增量同步（需要保持 WEREAD_API_KEY 环境变量）
crontab -e
# 添加：
0 7 * * * export WEREAD_API_KEY=wrk-xxx && cd /path/to/weread-notes-export-skill && python3 scripts/daily_sync_weread.py
```

同步脚本筛选最近 48 小时内有更新的书自动导出，已有的书不会重复处理。

---

## 同名书说明

笔记文件按书名保存（`书名.md`）。书架上同名不同版本的书（如不同译者的《1%法则》）导出时共享同一文件，后导出的会覆盖先导出的内容。如需保留多个版本，请将已有文件改名后重新导出。

---

## 配置

| 环境变量 | 必需 | 说明 |
|----------|------|------|
| `WEREAD_API_KEY` | ✅ | 微信读书 API Key（格式 `wrk-xxx`） |
| `WEREAD_NOTES_DIR` | ❌ | 笔记输出目录（默认 `~/.weread-notes/`） |

---

## 常见问题

| 问题 | 原因 | 解决 |
|------|------|------|
| `API_KEY 未设置` | 未配置环境变量 | `export WEREAD_API_KEY=wrk-xxx` |
| `请求太频繁` | API 限流 | 等待 10 秒后重试 |
| `书名未找到` | 书名不精确 | 用 --list 先查看准确书名 |
| 文件写入失败 | 权限/磁盘满 | `df -h` 检查磁盘 |

---

## 跨平台兼容

本 Skill 已上架 [skills.sh](https://www.skills.sh) 生态。通过环境变量配置，不硬编码平台路径，支持：

| 平台 | 说明 |
|------|------|
| 🤖 Hermes Agent | 完整支持 |
| 🦎 OpenClaw | 完整支持 |
| 💼 Workbuddy | 支持 SKILL.md 格式 |
| 🟢 Claude Code | 支持 SKILL.md 格式 |
| 🔵 Codex / Cursor | 支持 SKILL.md 格式 |

---

## 独立性边界（铁律）

本 Skill 是**完全独立**的开源组件，与任何具体应用（如「回音笔记」读书笔记站等）**零耦合**：

- ✅ **不 import** 任何应用项目模块（不依赖 weread_server.py / book_builder.py 等）
- ✅ **不读写** 任何应用数据目录（不触碰应用的用户目录、公共图书库缓存）
- ✅ **不调用** 任何应用构建流程（不触发 build.py / 发布脚本）
- ✅ **不硬编码** 平台路径（输出目录通过 `WEREAD_NOTES_DIR` 配置，默认 `~/.weread-notes/`）
- ✅ **纯标准库**：仅 `urllib` / `json` / `re` / `os` / `sys` / `time`，无第三方依赖

> 本 Skill 只做一件事：**输入 API Key + 输出读书笔记文件**（md / json）。下游消费方（数据分析、网页展示、多用户同步）自行读取产物，Skill 不感知它们的存在。
>
> 如果某个应用需要"按用户导出 / 公共书库缓存 / 服务端集成"，请在**应用自身**实现（复用或移植本 Skill 的算法思路），不要反过来让 Skill 依赖应用。

---

## 项目结构

```
weread-notes-export-skill/
├── .gitignore
├── CHANGELOG.md
├── LICENSE
├── README.md
├── SKILL.md
├── scripts/
│   ├── export_weread_notes.py        # 🎯 核心导出引擎
│   ├── daily_sync_weread.py          # 每日增量同步
│   └── format_notes.py               # 批量格式整理
└── templates/
    └── weread.env.template           # 环境变量模板
```

---

## 许可证

MIT
