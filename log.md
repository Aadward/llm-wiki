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
## [2026-05-13] create | OpenCode 调研材料入库
- 新增 entities/opencode.md — OpenCode 实体页（159k Stars、架构、四种运行形态、多模型支持）
- 新增 concepts/opencode-server-guidebook.md — 服务器部署与生产工作流实操手册（systemd/Skills/MCP/Hermes集成）
- 新增 summaries/opencode-research-summary.md — 调研总结（证据链、关键发现、可信度评估）
- 新增 raw/articles/opencode-research-2026-05.md — 调研原始素材（20+ 来源列表）
- 更新 index.md — 添加 OpenCode 相关条目（Entities + OpenCode 章节 + Summaries）
- 调研结论：核心内容可靠，安装命令和服务器模式有官方+多重第三方证据
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

## [2026-05-11] study | B2 多 Agent 协作模式对比
- 核心发现：OpenClaw CEO Pattern vs Hermes Kanban Orchestrator；共享竞态问题（edit 静默失败）；n8n Proxy Pattern 凭证隔离是 Hermes 最缺失的；Agent 间通信协议均未文档化
- 新增 concepts/multi-agent-collaboration.md — 三种协作模式 + 竞态问题 + 6 条知识断层

## [2026-05-11] study | B3 安全模型横向对比
- 核心发现：fail-closed vs default-allow+callback 哲学对立；审批时机差异（配置阶段 vs 运行时）；n8n Proxy Pattern 凭证永不外泄是 Hermes 最缺失的；secret redaction 层面不同（输出层 vs 环境层）
- 新增 concepts/security-model-comparison.md — 安全哲学对比 + 审批模型 + 凭证管理 + 8 条知识断层

## [2026-05-11] study | B4 成本优化策略对比
- 核心发现：Hermes 优化 token 量，OpenClaw 优化缓存命中率；Auxiliary Models 节省 40-60% 但依赖任务可拆分；日志膨胀解决思路不同（Context Compressor vs JSONL compaction）
- 新增 concepts/cost-optimization-comparison.md — 成本结构对比 + Auxiliary Models + 5 条知识断层

## [2026-05-11] study | C3 FTS5 全文检索原理
- 核心发现：FTS5 在 Hermes 中服务 L2 Episodic Memory 检索；与 OpenClaw SQLite-vec 的关键差异（BM25 无语义 vs 向量语义）；session_search 弥补语义不足；BM25 理解同义词的局限
- 新增 concepts/fts5-full-text-search.md — BM25 原理 + 局限性 + session_search 补偿 + 3 条知识断层

## [2026-05-11] study | C1 MCP 协议深度理解
- 核心发现：MCP 本质是"USB 标准"（解耦 Agent 和外部系统）；白名单局限（MCP Server 被控则白名单失效）；分层防御是唯一出路；最佳实践理念（最小暴露原则）
- 新增 concepts/mcp-protocol-deep-dive.md — USB 类比 + 白名单局限 + 分层防御 + 5 条知识断层

## [2026-05-11] study | C2 ReAct Loop 范式
- 核心发现：ReAct 推理+执行交替进行；OpenClaw vs Hermes 的 Loop 实现差异（显式 vs 隐式）；根本性局限（无依赖声明机制，复杂依赖链不稳定）
- 新增 concepts/react-loop-paradigm.md — ReAct 原理 + 两系统对比 + 3 条知识断层

## [2026-05-11] batch commit | D 轴专题跳过说明 + 收尾

## [2026-05-11] lint + reorganize | Wiki 阶段性整理：去重 + 关联连接 + 阶段总结
### 发现的问题
1. **技能系统重复**（×2）：
   - `hermes-agent-skills-system.md` — 底层机制（触发条件、生成流程、Curator 生命周期）
   - `hermes-agent-skill-library.md` — 官方生态（648 技能、CLI 命令、使用示例）
   - **处理**：在 skills-system.md 添加"本文定位说明"，明确两者互补关系

2. **记忆系统重复**（×3）：
   - `memory-knowledge-systems.md` — OpenClaw 方案（Layer 1-4）
   - `hermes-agent-memory-architecture.md` — Hermes 方案（4 层架构）
   - `memory-systems-comparison.md` — 深度对比（7 维度）
   - **处理**：在 memory-knowledge-systems.md 添加"系列导航：记忆系统三篇"，三篇合一系列

3. **Flows 重复**（×2）：
   - `openclaw-2026-background-tasks-flows.md` — 后台任务（任务感知与恢复）
   - `openclaw-flows-cli.md` — Flows CLI（线性设计哲学、SQLite 分类账）
   - **处理**：在 background-tasks-flows.md 的 Flows CLI 章节添加"详见：[[openclaw-flows-cli]]"

4. **Multi-Agent 重复**（×2）：
   - `multi-agent-orchestration.md` — 通用模式（CEO Pattern、n8n Proxy）
   - `multi-agent-collaboration.md` — OpenClaw ↔ Hermes 深度对比（3 种协作模式、竞态问题）
   - **处理**：在 multi-agent-orchestration.md 的 Related 段添加深度对比链接

### index.md 全面重构
- 新增**学习阶段总结**表格（A/B/C/D 四轴），快速把握学习脉络
- 重新分组：**Hermes 深度原理** → **Hermes 配置与集成** → **OpenClaw ↔ Hermes 对比** → **OpenClaw 核心机制** → **跨框架通用概念** → **版本演进** → **安全与凭证**
- 每个分组内的重复项添加 `[[★ 说明]]` 注释，指明每篇的独特价值
- Flows 和 Multi-Agent 体系分组更清晰（各自独立分组而非混入跨框架）
- **Total pages: 72 → 76**（新增 4 个定位说明/系列导航段落）
- 相关概念章节添加了明确的"关联"和"系列"导航标记


## [2026-05-12] study | 新版本扫描: OpenClaw v2026.4.14/3.28 + Hermes v0.8/0.10
- 研究发现:自上次学习(2026-05-11)后共发现 3 个新版本值得记录
- 新增 raw/articles/openclaw-v2026-4-14-release.md — v2026.4.14 原始素材
- 新增 raw/articles/openclaw-v2026-3-28-release.md — v2026.3.28 原始素材(高危安全修复)
- 新增 raw/articles/hermes-agent-v0-8-v0-10-release.md — Hermes v0.8/v0.10 原始素材
- 新增 concepts/openclaw-v2026-4-14-release.md — 安全加固+可靠性+性能,89项修复
- 新增 concepts/openclaw-v2026-3-28-release.md — 路径遍历漏洞修复,高危安全
- 新增 concepts/hermes-agent-v0-8-v0-10-release.md — Live Model Switching,国内直连
- 冲突检测:无冲突(均为版本演进内容,与现有知识体系无重叠)
- 更新 index.md — Total pages: 76 → 79


## [2026-05-10] study | Version scan: OpenClaw v2026.4.23/4.24 + Hermes v0.12.0 + EvoMap controversy
- Research found important new versions and controversy event since last study (2026-05-12)
- New raw/articles/openclaw-v2026-4-23-release.md -- v2026.4.23 raw (GPT-5.5/subagent)
- New raw/articles/openclaw-v2026-4-24-release.md -- v2026.4.24 raw (DeepSeek V4/Google Meet)
- New raw/articles/hermes-agent-v0-12-release.md -- Hermes v0.12.0 raw (Curator Release)
- New concepts/openclaw-v2026-4-23-release.md -- GPT-5.5 + subagent branch context
- New concepts/openclaw-v2026-4-24-release.md -- DeepSeek V4 as default (17x cost reduction) + plugin perf leap
- New concepts/hermes-agent-v0-12-release.md -- Autonomous Curator, 1096 commits
- New concepts/hermes-agent-evolution-controversy.md -- EvoMap accuses Hermes of architectural-level plagiarism
- CONFLICT REPORT: Hermes EvoMap plagiarism controversy
  - EvoMap (2026-04-15) accuses Hermes self-evolution module (10-step loop / 3-layer memory / 12 term replacements) of high structural isomorphism with Evolver
  - Hermes official responded Delete your account then blocked the accuser; no resolution yet
  - Hermes v0.12 (2026-04-30) released Curator feature (during controversy period)
- Updated index.md -- Total pages: 79 -> 83
- Created openclaw-best-practices.md — 42-case Cookbook distillation: anti-patterns, multi-agent, n8n security, self-healing infra patterns
- Created hermes-agent-failure-modes.md — 10 failure modes covering whole-turn commit, parallel tool deps, Skills Index inflation, cron stateless, callback black-box, Context Compressor unknowns
- Created framework-selection-guide.md — merged decision-tree + comparison into definitive selection guide with data table, decision checklist, hybrid usage pattern
- Archived: openclaw-hermes-decision-tree.md, openclaw-hermes-comparison.md, openclaw-v2026-4-14/23/24-release.md (raw + concepts) — content merged into framework-selection-guide.md
- Pages: 91 concepts + 52 raw

## [2026-05-12] ingest | Hermes Agent best practices + hot use cases research
- Research: surveyed 10+ Chinese and English sources on Hermes Agent best practices and use cases
- Updated concepts/hermes-agent-best-practices.md — added 4 new sections:
  - 四、热门 Use Cases 精选（99 社区案例精华）— 5 标杆案例、分类分布表
  - 五、Hermes + GPT-5.5 最强组合模式 — 能力互补矩阵
  - 六、中文生态集成 — 微信/飞书/钉钉/元宝
  - 七、工程成熟度 Checklist（2026-05 更新）
  - Anti-Patterns 反模式 — 5 个社区反馈效果差的做法
- Created 3 new usecases/ pages:
  - usecases/hermes-multi-agent-dev.md — Teknium 12 并行实例
  - usecases/hermes-family-whatsapp.md — 家庭共享 Agent
  - usecases/hermes-autonomous-movie.md — 自主电影生成
- Updated index.md — added Hermes Use Cases section, Total pages: 83 → 87
- Key insight: Hermes 的价值在于「用得越久越聪明」，最佳实践本质是找到高重复性工作流让技能沉淀


## [2026-05-15] ingest | NemoClaw 调研入库

**调研覆盖**：NVIDIA/NemoClaw GitHub（20,417 ⭐）、awesome-nemoclaw 社区生态、官方文档（架构、推理选项、网络策略、沙箱加固、生命周期管理）

**调研重点**：
- 核心能力：沙箱三层隔离（Landlock + seccomp + netns）、推理路由（credentials never in sandbox）、声明式网络策略、Blueprint 版本化管理
- 关键配置：安装（`curl -fsSL https://nvidia.com/nemoclaw.sh | bash`）、onboard 向导、策略层级（Restricted/Balanced/Open）
- 最佳实践：OpenShell 生命周期边界、凭证管理、升级快照、Landlock 验证、多沙箱端口隔离
- 生态资源：VoltAgent/awesome-nemoclaw 社区 preset（覆盖 20+ 服务）

**创建文件**：
- `raw/articles/nvidia-nemoclaw-2026.md` — 原始材料存档
- `entities/nemoclaw.md` — NemoClaw 实体页（概述、Provider、CLI 命令、生态）
- `concepts/nemoclaw-architecture.md` — NemoClaw 架构深度解析（分层拓扑、凭证路由、设计原则）
- `concepts/nemoclaw-best-practices.md` — NemoClaw 最佳实践（安全边界、升级回滚、策略管理、反模式）

**更新 index.md**：添加 NemoClaw 实体 + 3 个概念页到安全与凭证章节，Total pages: 87 → 91
