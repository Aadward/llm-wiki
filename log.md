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