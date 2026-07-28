---
name: weread-notes-export
description: 微信读书笔记导出 — 按章节组织划线/评论，支持同名合并、安全文件名、每日同步
version: 1.2.0
homepage: https://github.com/dongwei6688/weread-notes-export-skill
license: MIT
metadata:
  hermes:
    tags: [weread, reading, notes, export, chinese]
    related_skills: [reading-notes-manager]
---

# WeRead Notes Export — 微信读书笔记导出

把微信读书的**划线（书签）**和**想法（评论/批注）**按**章节树**导出为结构化 Markdown 文件。支持增量同步、同名合并、安全文件名。

## 配置

```bash
export WEREAD_API_KEY=wrk-xxxxxxxx
```
API Key 获取方式：打开 https://weread.qq.com/r/weread-skills → 获取 API Key → 微信扫码。

## Decision Guide

| 用户需求 | 命令 |
|----------|------|
| 查看导出统计 | `python3 scripts/export_weread_notes.py --stats` |
| 批量导出全部 | `python3 scripts/export_weread_notes.py --all` |
| 导出单本书 | `python3 scripts/export_weread_notes.py --book "书名"` |
| 查看最近更新 | `python3 scripts/export_weread_notes.py --recent` |
| 列出所有有笔记的书 | `python3 scripts/export_weread_notes.py --list` |
| 每日增量同步 | cron: `python3 scripts/daily_sync_weread.py` |

## 导出格式

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
- 💬 **评论跟随** — 划线+评论作为一个整体，分隔线在评论下方
- 🏷️ **安全文件名** — 自动替换 `:`→`：`、`|`→`·`、`/`→`／`、`\`→`＼`
- 🔗 **同名合并** — 同名不同作者的书分别保存

## 输出目录

默认 `~/.weread-notes/`，可通过 `WEREAD_NOTES_DIR` 环境变量自定义。

## 每日自动同步

```bash
0 7 * * * cd /path/to/weread-notes-export && python3 scripts/daily_sync_weread.py
```
筛选最近 48 小时内有更新的书自动导出，已有的书不会重复处理。

## 操作规范

### API 分页
`/user/notebooks` 默认只返回 200 条，但用户可能有上千本有笔记的书。**必须**传 `count: 2000` 并用 `totalBookCount + hasMore` 回退兜底。

### 章节树过滤
只过滤 `uid <= 2`（封面 uid=1，版权页 uid=2），保留 uid≥3 的内容章节。

### 同名书合并
同作者按章节合并，不同作者分别保存为 `书名（作者）.md`。作者名比较支持子串匹配。

### 分隔线规则
- 带评论的划线 → 划线+评论为一个整体，分隔线在评论下方
- 章节标题前 → 不加分隔线，只留空行
