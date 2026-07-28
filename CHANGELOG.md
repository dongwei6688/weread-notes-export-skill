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

# 更新日志

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
