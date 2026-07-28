---
name: weread-notes-export
description: 微信读书笔记导出 — 按章节组织划线/评论，支持同名合并、分隔线格式、每日同步
version: 1.1.0
homepage: https://github.com/dongwei6688/weread-notes-export-skill
license: MIT
setup_needed: false
metadata:
  hermes:
    tags: [weread, reading, notes, export, chinese]
    related_skills: [reading-notes-manager]
  openclaw:
    emoji: 📚
    requires:
      bins:
        - python3
    install: []
---

# WeRead Notes Export — 微信读书笔记导出 Skill

把微信读书的**划线（书签）**和**想法（评论/批注）**按**章节树**导出为本地的结构化 Markdown 文件。支持增量同步、同名合并、安全文件名、分隔线格式。

## 依赖

无外部依赖。脚本使用纯 Python 3 标准库，直接调用微信读书官方 API。

## 安装

**推荐（跨平台，兼容 Hermes / OpenClaw / Workbuddy / Claude Code 等）：**

```bash
npx skills add https://github.com/dongwei6688/weread-notes-export-skill --skill weread-notes-export
```

**手动安装（Hermes Agent / OpenClaw 等）：**

```bash
git clone https://github.com/dongwei6688/weread-notes-export-skill.git
# 把克隆下来的目录放进 Agent 的 skills 目录即可
```

## 配置

```bash
# 必需：微信读书 API Key
export WEREAD_API_KEY=wrk-xxxxxxxx
```

### 获取 API Key

打开 https://weread.qq.com/r/weread-skills → 点击**获取 API Key** → 微信扫码 → 复制 Key。

或在微信读书 App → **设置** → 底部获取 API Key（扫码或复制 `wrk-xxx`）。

## 快速开始

```bash
# 查看统计
python3 scripts/export_weread_notes.py --stats

# 导出一本书
python3 scripts/export_weread_notes.py --book "原则"

# 导出全部
python3 scripts/export_weread_notes.py --all
```

## 命令参考

| 命令 | 说明 |
|------|------|
| `--stats` | 统计：有笔记的书总数、划线数、笔记数、想法数 |
| `--list` | 列出所有有笔记的书 |
| `--recent` | 查看最近 7 天更新过的书 |
| `--book <书名/ID>` | 按书名或 bookId 导出单本 |
| `--all` | 全量导出所有有笔记的书 |

## 输出目录

默认输出到 `~/.weread-notes/`，可通过 `WEREAD_NOTES_DIR` 环境变量自定义。

## 输出格式

```markdown
# 《原则》读书笔记

## 导言

> 不管我一生中取得了多大的成功...

---

> 独立思考并决定...

💬 这个观点很实用

---
```

- 📂 **章节组织** — 按书籍章节树归类
- 📏 **分隔线** — 条目间自动加 `---`
- 💬 **评论跟随** — 划线+评论作为一个整体
- 🏷️ **安全文件名** — 自动替换 `:`、`|` 等特殊字符
- 🔗 **同名合并** — 同名不同作者的书分别保存

## 每日自动同步

```bash
# 添加到 crontab（每天早上 7 点）
# npx skills add 安装:
0 7 * * * cd ~/.agents/skills/weread-notes-export && python3 scripts/daily_sync_weread.py
# 手动安装（替换为实际路径）:
# 0 7 * * * cd /path/to/weread-notes-export && python3 scripts/daily_sync_weread.py
```

同步脚本会筛选最近 48 小时内有更新的书自动导出，已有的书不会重复处理。

## 脚本说明

| 脚本 | 功能 |
|------|------|
| `scripts/export_weread_notes.py` | 核心导出引擎：按章节树组织、同名合并、分隔线格式 |
| `scripts/daily_sync_weread.py` | 每日增量同步脚本（配合 cron） |
| `scripts/format_notes.py` | 批量格式整理（为已有笔记文件添加分隔线） |

## 操作规范与陷阱

### API 分页
`/user/notebooks` 默认只返回 200 条，但用户可能有上千本有笔记的书。
**必须**传 `count: 2000` 并用 `totalBookCount + hasMore` 做回退兜底。

### 章节树过滤
构建章节树时，只过滤 `uid <= 2`（封面 uid=1，版权页 uid=2），保留 uid≥3 的内容章节。

### 同名书合并
同名不同作者的书：同作者按章节合并，不同作者分别保存为 `书名（作者）.md`。
作者名比较支持子串匹配（"毛姆" ⊂ "威廉·萨默赛特·毛姆"）。

### 安全文件名
| 字符 | 替换为 | 原因 |
|------|--------|------|
| `:` | `：` | Windows 禁止 |
| `\|` | `·` | Windows 禁止 |
| `/` | `／` | 文件系统路径分隔符 |
| `\` | `＼` | Windows 路径分隔符 |

存取用同一套 `make_safe_filename()` 函数。

### 分隔线格式
- 带评论的划线 → 划线+评论为一个整体，分隔线在评论下方
- 章节标题前 → 不加分隔线，只留空行

## 跨平台兼容

本 Skill 已上架 [skills.sh](https://www.skills.sh) 生态，可通过 `npx skills add` 一键安装。使用环境变量（`WEREAD_API_KEY`、`WEREAD_NOTES_DIR`）配置，不硬编码平台路径，兼容 Hermes / OpenClaw / Workbuddy / Claude Code / Codex / Cursor 等。

## 开源协议

MIT
