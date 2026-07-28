---
name: weread-notes-export
description: 微信读书笔记导出 — 按章节组织划线/评论，支持同名合并、安全文件名、每日同步
version: 1.2.2
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

## Decision Guide

| 用户需求 | 命令 |
|----------|------|
| 查看导出统计 | `python3 scripts/export_weread_notes.py --stats` |
| 批量导出全部 | `python3 scripts/export_weread_notes.py --all` |
| 导出单本书 | `python3 scripts/export_weread_notes.py --book "书名"` |
| 查看最近更新 | `python3 scripts/export_weread_notes.py --recent` |
| 列出所有有笔记的书 | `python3 scripts/export_weread_notes.py --list` |
| 每日增量同步 | `python3 scripts/daily_sync_weread.py` |

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
- 🏷️ **安全文件名** — `:`→`：`、`|`→`·`、`/`→`／`、`\`→`＼`
- 🔗 **同名合并** — 同名不同作者的书分别保存，作者支持子串匹配

## 关键算法

### 1. API 分页回退
```python
# /user/notebooks 默认 limit=200，用户可能有 1000+ 本
params = {"count": 2000, "offset": 0, "limit": 200}
data = api_get("/user/notebooks", params)
all_books = data["notebooks"]
# 如果 totalBookCount > 返回数，循环翻页直到取完
while len(all_books) < data.get("totalBookCount", len(all_books)):
    params["offset"] += 200
    data = api_get("/user/notebooks", params)
    all_books.extend(data["notebooks"])
```

### 2. 章节树构建
```python
def build_chapter_tree(chapters):
    # uid=1 封面, uid=2 版权页 — 跳过
    chapters = [c for c in chapters if c["chapterUid"] >= 3]
    # 按 chapterUid 排序，递归建树
    tree = {}
    for c in sorted(chapters, key=lambda x: x["chapterUid"]):
        parent = c.get("parentUid", 0)
        if parent == 0:
            tree[c["title"]] = {"data": c, "children": {}}
        else:
            # 挂到父章节下
            parent_node = find_node(tree, parent)
            if parent_node:
                parent_node["children"][c["title"]] = {"data": c, "children": {}}
    return tree
```

### 3. 评论匹配（4 种策略）
```python
# 给每条评论找到对应章节，按优先级降序尝试：
def match_comment(comment, chapters, chapter_tree):
    # ① chapterUid 精确匹配
    if comment.get("chapterUid"):
        return chapters.get(comment["chapterUid"])
    # ② chapterName 匹配（全角空格兼容）
    name = comment.get("chapterName", "").replace(" ", "　")
    for c in chapters:
        if normalize_space(c["title"]) == normalize_space(name):
            return c
    # ③ abstract 关键词匹配章节标题
    abstract = comment.get("abstract", "")
    for c in chapters:
        if c["title"] in abstract:
            return c
    # ④ 章节树路径模糊匹配
    return fuzzy_match_in_tree(comment, chapter_tree)
```

### 4. 同名书合并
```python
# 同名不同作者 → 分别保存为 书名(作者).md
files = {}
for book in books:
    key = (book["title"], book.get("author", ""))
    if key in files:
        files[key]["notes"].extend(book["notes"])
    else:
        # 作者名为子串匹配：如果已存在同书不同作者，区分
        existing = [k for k in files if k[0] == book["title"]]
        if existing and not is_same_author(existing[0][1], book.get("author", "")):
            filename = f"{safe(book['title'])}（{safe(book['author'])}）.md"
        else:
            filename = f"{safe(book['title'])}.md"
        files[key] = {"filename": filename, "notes": book["notes"]}
```

## 输出目录

默认 `~/.weread-notes/`，通过 `WEREAD_NOTES_DIR` 自定义。

## 每日自动同步

```bash
0 7 * * * cd <skills_dir> && python3 scripts/daily_sync_weread.py
```
筛选 48h 内有更新的书自动导出，不重复处理已有笔记。

## 常见错误处理

| 错误 | 原因 | 处理 |
|------|------|------|
| `WEREAD_API_KEY` 未设置 | 环境变量缺失 | 检查配置 |
| API 返回 401 | Key 过期或无效 | 重新获取 Key |
| `chapterUid` 为空 | 旧版微信读书接口变化 | 仅用 chapterName 匹配 |
| 文件写入失败 | 权限/磁盘满 | 检查 `WEREAD_NOTES_DIR` 目录权限 |
| 同作者名不同写法 | 同一作者出现"王小波"和"小波" | 子串匹配比 `==` 更宽容 |
