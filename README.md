# 📚 WeRead Notes Export — 微信读书笔记导出 Skill

把微信读书的**划线（书签）**和**想法（评论/批注）**按**章节树**导出为本地结构化 Markdown 文件。支持增量同步、同名合并、安全文件名。

## ✨ 亮点

### 按章节树组织，不是简单平铺
从 API 获取书籍的完整章节结构，每条划线自动归入所属章节。一本书的笔记就是一个**有层次的 Markdown 文件**。

### 同名不同作者，自动分开保存
同作者合并，不同作者分别保存为 `书名（作者）.md`，不会混在一起。

### 安全文件名处理
书名中的特殊字符自动替换为兼容字符，**Windows / Linux / macOS 全平台通用**。

### 条目间自动加分隔线
每条划线之间自动插入 `---` 分隔线，带评论的划线+评论作为一个整体。

### 增量同步，适合每日 cron
跑一次 `--all` 全量导出后设个 cron 每天增量同步，只导出最近 48 小时内有更新的书。

### 零外部依赖
纯 Python 3 标准库（`urllib` + `json`），无需 `pip install` 任何包。有 Python 就行。

### 跨平台安装（已上架 skills.sh）
```bash
npx skills add dongwei6688/weread-notes-export-skill
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

**推荐（跨平台，兼容 Hermes / OpenClaw / Workbuddy / Claude Code 等）：**

```bash
npx skills add dongwei6688/weread-notes-export-skill
```

**手动安装：**

```bash
git clone https://github.com/dongwei6688/weread-notes-export-skill.git
# 把目录放进 Agent 的 skills 目录即可
```

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

---

## 每日自动同步

```bash
# 添加到 crontab（每天早上 7 点）
# npx skills add 安装:
0 7 * * * cd ~/.agents/skills/weread-notes-export/scripts && python3 daily_sync_weread.py
# 手动安装（替换为实际路径）:
# 0 7 * * * cd /path/to/skill/scripts && python3 daily_sync_weread.py
```

同步脚本筛选最近 48 小时内有更新的书自动导出，已有的书不会重复处理。

---

## 同名书合并策略

1. **同作者** → 按章节合并，保留两个版本的笔记内容
2. **不同作者** → 分别保存为 `书名（作者A）.md` 和 `书名（作者B）.md`
3. 作者简称自动匹配（如"毛姆"⊂"威廉·萨默赛特·毛姆"）

---

## 配置

| 环境变量 | 必需 | 说明 |
|----------|------|------|
| `WEREAD_API_KEY` | ✅ | 微信读书 API Key（格式 `wrk-xxx`） |
| `WEREAD_NOTES_DIR` | ❌ | 笔记输出目录（默认 `~/.weread-notes/`） |

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

## 项目结构

```
weread-notes-export/
├── SKILL.md                          # 技能定义
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
