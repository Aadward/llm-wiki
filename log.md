# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete
> When this file exceeds 500 entries, rotate: rename to log-YYYY.md, start fresh.

## [2026-05-08] create | Wiki initialized
- Domain: 个人知识管理 & AI 工具学习
- Structure created with SCHEMA.md, index.md, log.md
- Initial ingest: OpenClaw Cookbook (42 use cases)

## [2026-05-08] update | Wiki reorganization
- Restructured use cases: moved from `concepts/usecases/` to `usecases/`
- Rewrote all 42 use case pages as lightweight index pages (frontmatter + summary + link to raw, no full content duplication)
- Created `usecases/index.md` with category navigation
- Added 2 new concept pages: `persistent-agent-patterns.md`, `alert-center-patterns.md`
- Rewrote `index.md` as full navigation page with table of contents
- Fixed broken links: `[[infra]]` → `[[persistent-agent-patterns]]`, `[[raw/articles/]]` → plain text
- Deleted `concepts/usecases/` (had full duplicate content)
## [2026-05-08] translate | openclaw-cookbook-summary → 中文
- 将 summaries/openclaw-cookbook-summary.md 全文翻译为中文
- 保留所有章节结构、表格、代码块
- 更新 frontmatter updated 日期

## [2026-05-08] ingest | OpenClaw 2026.3-4.x 版本更新
- 新增 raw/articles/openclaw-2026-plugin-sdk-overhaul.md — 插件系统重构详情
- 新增 raw/articles/openclaw-2026-background-tasks-flows.md — 后台任务与 Flows CLI 详情
- 新增 concepts/openclaw-2026-plugin-sdk.md — 插件SDK重构概念页
- 新增 concepts/openclaw-2026-background-tasks-flows.md — 后台任务流概念页
- 更新 index.md — 补充 2 个新 Core Concepts 条目
