# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete
> When this file exceeds 500 entries, rotate: rename to log-YYYY.md, start fresh.

## [2026-05-08] create | Wiki initialized
- Domain: 个人知识管理 & AI 工具学习
- Structure created with SCHEMA.md, index.md, log.md
- Initial ingest: OpenClaw Cookbook (42 use cases)

## [2026-05-09] ingest | Hermes Agent 深度调研：最佳实践模式总结
- 新增 entities/hermes-agent.md — Hermes Agent 实体页（概述、定位、对比）
- 新增 concepts/hermes-agent-learning-loop.md — 闭环学习系统与 GEPA 引擎
- 新增 concepts/hermes-agent-memory-architecture.md — 四层记忆架构详解
- 新增 concepts/hermes-agent-skills-system.md — 技能系统机制
- 新增 concepts/hermes-agent-best-practices.md — 工程师视角最佳实践（含 Use Cases 分层、成本优化、安全配置）
- 新增 raw/articles/hermes-agent-overview-2026.md — 官方概览原始素材
- 新增 raw/articles/hermes-agent-use-cases-2026.md — 官方 User Stories 原始素材（99 个案例）
- 更新 index.md — 添加 Hermes Agent 相关条目
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

## [2026-05-09] lint + fix | Wiki 结构全面修复
- 47 个 raw/articles/*.md 文件补充 frontmatter（source_url、ingested、sha256）
- 2 个 placeholder sha256 文件（hermes-agent-overview/use-cases）重新计算真实哈希
- 42 个 usecases/*.md 文件修正 frontmatter 字段名：source: → sources:
- index.md 全面更新：补充 10 个新 Core Concepts 条目（h1-h7 + o1-o3），重构分类结构（OpenClaw / Hermes / 跨框架），补充 Raw Sources 条目，更新 Last updated + Total pages
- 本次新增 10 个概念页面：
  - concepts/hermes-agent-source-code-architecture.md
  - concepts/hermes-agent-mcp-integration.md
  - concepts/hermes-agent-soul-agents-md.md
  - concepts/hermes-agent-voice-mode.md
  - concepts/hermes-agent-profiles-multi-instance.md
  - concepts/hermes-agent-skill-library.md
  - concepts/hermes-agent-troubleshooting.md
  - concepts/openclaw-source-code-architecture.md
  - concepts/openclaw-hermes-comparison.md
  - concepts/openclaw-clawhub.md

## [2026-05-09] ingest | OpenClaw v2026.4.5 + v2026.3.11 + Hermes Agent v0.12.0 版本学习
- 新增 raw/articles/openclaw-v2026-4-5-release.md — v2026.4.5 原始素材（多媒体、/dreaming、Prompt Cache）
- 新增 raw/articles/openclaw-v2026-3-11-release.md — v2026.3.11 原始素材（安全强化、Immutable Release）
- 新增 raw/articles/hermes-agent-v0-12-release.md — Hermes v0.12.0 原始素材（斜杠指令、多会话）
- 新增 concepts/openclaw-v2026-4-5-release.md — OpenClaw v2026.4.5 概念页
- 新增 concepts/openclaw-v2026-3-11-release.md — OpenClaw v2026.3.11 安全版概念页
- 新增 concepts/hermes-agent-v0-12-release.md — Hermes v0.12.0 版本演进页
- 更新 index.md — 添加 3 个新 Core Concepts 条目，Total pages: 69 → 72
- 冲突检测：无冲突（两项目定位清晰，无重叠覆盖）

## [2026-05-11] study | A1 GEPA 引擎深度研究
- 研究目标：理解 Hermes GEPA 闭环学习的完整机制
- 核心发现：
  - GEPA 存在两个抽象层级：宏观层（Goal-Evaluation-Plan-Action）vs 微观层（Gather-Execute-Process-Assess）
  - 技能生成触发条件：工具调用 ≥ 5 次 + 任务成功
  - Skill 设计选择：存储"解题模式"而非"代码片段"，本质是推迟编译到运行时
  - 与 OpenClaw Heartbeat 的本质差异：被动事后聪明 vs 主动定时巡检
- 诚实列出 6 条知识断层
- 更新 concepts/hermes-agent-learning-loop.md — 全面重写，整合源码架构文档

## [2026-05-11] study | A2 Hermes AIAgent 消息循环
- 核心发现：同步循环（有意设计）vs 异步；toolset__tool_name 扁平命名空间；并行工具调用隐含依赖问题；Skills Index 累积性 token 膨胀；整轮提交记忆模式；四个回调机制文档最不透明
- 新增 concepts/hermes-agent-message-loop.md — 消息循环完整流程图 + 7 个知识点

## [2026-05-11] study | A3 OpenClaw Gateway 架构
- 核心发现：Gateway 纯路由层（不产生智能）vs Hermes Engine-First；WebSocket 实时双向设计意图；八大 MD 文件注入体系；Flows CLI 任务可见性是 Hermes 最缺失的能力；安全哲学：OpenClaw fail-closed vs Hermes 默认允许
- 新增 concepts/openclaw-gateway-architecture.md — Gateway 三层架构 + Flows + 安全对比 + 6 条知识断层

## [2026-05-11] study | A4 OpenClaw Flows CLI
- 核心发现：线性是有意选择；SQLite 分类账是运维思维而非功能；与 Hermes Cron 本质差异（可见性 vs 自动完成）；doctor 修复的"任务损坏是常态"哲学
- 新增 concepts/openclaw-flows-cli.md — Flows 设计哲学 + 分类账 + 6 条知识断层

## [2026-05-11] study | A5 两者记忆系统对比
- 核心发现：记忆的运行时状态 vs 文件系统状态；Hermes 整轮提交 vs OpenClaw Append-only；FTS5 vs SQLite-vec 检索差异；USER.md 独立文件 vs 混在 MEMORY.md；主动性哲学对立（系统自动管 vs 人类直接掌控）
- 新增 concepts/memory-systems-comparison.md — 7 个对比维度 + 6 条知识断层

## [2026-05-11] study | B1 选型决策树
- 核心发现：三条分叉轴（安全 vs 灵活、可见性 vs 自动化、技能人主 vs 机主）；一句话定位（掌控 vs 进化）；混合使用是被低估的场景（OpenClaw 网关 + Hermes 执行引擎）
- 新增 concepts/openclaw-hermes-decision-tree.md — 决策树 + 三轴分叉 + 混合架构 + 选型矩阵 + 5 条知识断层

## [2026-05-11] study | B2 多 Agent 协作模式对比 — 进行中
