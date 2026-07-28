# 📚 WeRead Notes Export — 微信读书笔记导出 Skill

把微信读书的**划线（书签）** 和**想法（评论/批注）** 按**章节树**导出为本地结构化 Markdown 文件。适配 Hermes Agent，支持增量同步、同名合并、安全文件名。

---

## ✨ 亮点

### 🎯 按章节树组织，不是简单平铺
从 API 获取书籍的完整章节结构，每条划线自动归入所属章节。一本书的笔记就是一个**有层次的 Markdown 文件**，而不是一长串无序的内容。

### 🔗 同名不同作者，自动分开保存
微信读书上同名书不少。本 Skill 会自动检测作者是否一致——同作者合并，不同作者分别保存为 `书名（作者）.md`，不会混在一起。

### 🛡️ 安全文件名处理
书名中的 `:`、`|`、`/`、`\` 等特殊字符自动替换为兼容字符，**Windows / Linux / macOS 全平台通用**，不会因为文件名非法导致同步失败。

### 📏 条目间自动加分隔线
每条划线之间自动插入 `---` 分隔线，带评论的划线+评论作为一个整体，视觉层次清晰，一目了然。

### ⏰ 增量同步，适合每日 cron
只需跑一次 `--all` 全量导出，之后设个 cron 每天自动增量同步，只导出最近 48 小时内有更新的书。

### 🐍 零外部依赖
纯 Python 3 标准库（`urllib` + `json`），无需 `pip install` 任何包。有 Python 就行。

---

## 快速开始

### 1. 获取 API Key

打开微信读书 → 设置 → 账户 → API Key，申请一个 `wrk-xxx` 格式的 Key。

```bash
export WEREAD_API_KEY=wrk-xxxxxxxx
```

### 2. 安装 Skill

```bash
# Hermes Agent
git clone https://github.com/dongwei6688/weread-notes-export-skill \
  ~/.hermes/skills/weread-notes-export
```

### 3. 导出笔记

```bash
# 查看统计
python3 ~/.hermes/skills/weread-notes-export/scripts/export_weread_notes.py --stats

# 导出一本书
python3 ~/.hermes/skills/weread-notes-export/scripts/export_weread_notes.py --book "原则"

# 导出全部
python3 ~/.hermes/skills/weread-notes-export/scripts/export_weread_notes.py --all

# 查看最近更新的书
python3 ~/.hermes/skills/weread-notes-export/scripts/export_weread_notes.py --recent
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
0 7 * * * cd /path/to/skill/scripts && python3 daily_sync_weread.py

# 或使用 Hermes cron
hermes cron create --schedule "0 7 * * *" \
  --prompt "运行每日微信读书笔记同步" \
  --skills weread-notes-export
```

同步脚本会筛选最近 48 小时内有更新的书自动导出，已有的书不会重复处理。

---

## 同名书合并策略

微信读书中同一本书可能存在多个版本（不同出版社/译者），本 Skill 的策略是：

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

## 项目结构

```
weread-notes-export/
├── SKILL.md                          # Hermes 技能清单
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
