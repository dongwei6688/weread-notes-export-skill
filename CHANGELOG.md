# 更新日志

## v1.3.1 (2026-08-11)

### 新增
- **`--json-only`**：仅输出 `.json` 不写 `.md`（每日增量同步可只产 JSON 供数据分析；`daily_sync_weread.py --json-only`）

## v1.3.0 (2026-08-11)

### 新增
- **`--json` 结构化导出**：`--book/--all/--json` 或 `daily_sync_weread.py --json` 时同时输出 `.json`（schema v1：书元信息 + 章节路径 + 划线/评论条目）。JSON 保留 md 中丢失的信息（划线 range、评论原始多行内容、独立想法 abstract），数据分析直接用 JSON 无需解析 Markdown；默认行为不变（仍输出 .md）

## v1.2.9 (2026-08-11)

### Docs
- **README/SKILL 与实现一致性修正**：删除不存在的"同名不同作者自动分开保存"（实际按书名保存、同名书共享文件）与 `--format` 参数描述，补充多行划线/评论/Unicode 行分隔符（U+2028/U+2029）健壮处理说明

## v1.2.8 (2026-08-11)

### Fixed
- **Unicode 行分隔符拆散划线**：微信读书部分划线/摘要含 U+2028/U+2029（Unicode 行分隔符），解析器按行分隔拆解后划线被拆成碎片（如《价值》"知识、能力和价值观，才是深藏于内心……"）。flatten_multi_line 与评论空行压缩现在统一替换 U+2028/U+2029 为换行再压平，划线/评论完整保留

## v1.2.7 (2026-08-11)

### Fixed
- **评论内空行拆散评论**：多行评论含分段空行时（如"正确收集必要素材的两个原则："后空一行再写具体内容），旧逻辑按空行截断，空行后的内容变成游离文本。新增评论空行压缩（`\n\s*\n+` → `\n`），bookmark 与 review_only 的评论内容均处理，评论块完整连续输出

## v1.2.6 (2026-08-11)

### Fixed
- **多行划线署名行掉队**：划线文本含换行时（如引文末尾带"——威廉·詹姆斯"），旧逻辑只给首行加 `>` 前缀，署名行变成游离文本。新增 `flatten_multi_line()` 将多行划线/摘要压成单行（换行 → 空格），bookmark 与 review_only 分支均生效
- **脚本版本号残留**：`SKILL_VERSION` 从 1.0.0 同步至 1.2.6（此前未随 CHANGELOG 更新）

## v1.2.5 (2026-07-29)

### Fixed
- 修复 LICENSE 文件内容损坏问题
- README 增加版本徽章
- 修复仓库名称一致性
- 完善目录结构（含 format_notes.py）

## v1.2.4 (2026-07-28)

### Changed
- 决策表移至配置前（Overview → Decision Guide → 配置，对齐 docx）
- 错误处理表操作指令具体化（不再写"检查配置"，写检错命令）
- crontab 路径改为 `~/.agents/skills/weread-notes-export`

## v1.2.3 (2026-07-28)
### Changed
- YAML 精简至 3 字段（name+description+license），对齐 anthropics 标准
- 标题改为纯英文 `# WeRead Notes Export`
- 新增 `## Overview` 概览节

## v1.2.2 (2026-07-28)

### Changed
- **对标 anthropics docx skill**：操作规范改为可执行算法代码（API 分页回退、章节树构建、评论 4 策略匹配、同名书合并）
- **新增错误处理表**：5 种常见错误原因与处理方式

## v1.2.1 (2026-07-28)

### Fixed
- **开发产物清理**：删除 crontab 节中的路径占位符 `/path/to/weread-notes-export`

## v1.2.0 (2026-07-28)

### Changed
- **去除冗余节**：删除"依赖"（无外部依赖）、"获取 API Key"用户指引（Agent 不需要）、"脚本说明"表格（与 Decision Guide 重复）
- **精简配置**：API Key 获取方式合并为一行说明
- **整体压缩**：SKILL.md 从 118 行精简至 82 行，保留 Agent 必需信息

## v1.1.0 (2026-07-28)

### Added
- 初始发布
