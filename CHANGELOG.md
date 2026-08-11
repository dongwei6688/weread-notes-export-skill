# 更新日志

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
