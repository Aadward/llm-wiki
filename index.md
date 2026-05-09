---
title: Wiki Index
summary: OpenClaw & LLM Agent 学习 Wiki 导航页
tags: [meta]
created: 2026-05-08
updated: 2026-05-11
---

# OpenClaw & Hermes Agent Wiki

> 用于 AI 工具学习、个人知识管理、Agent 工作流设计。
> 基于 [awesome-openclaw-usecases](https://github.com/hesamsheikh/awesome-openclaw-usecases) + OpenClaw Cookbook 构建。

---

## Entities（实体）

|- [[openclaw|OpenClaw]] — "AI that actually does things"，多渠道接入、任务可见性强、社区技能生态丰富（44,000+）
|- [[hermes-agent|Hermes Agent]] — "The agent that grows with you"，自我进化、GEPA 闭环学习、四层记忆架构

---

## 学习阶段总结（2026-05-11）

本次整理覆盖 **14 天学习成果**，共 **76 篇页面**，分为四个轴向：

| 轴向 | 主题 | 核心产出 |
|------|------|----------|
| **A 轴 · 深度原理** | GEPA 引擎 / 消息循环 / 源码架构 / SOUL.md 定制 | 理解 Hermes "Engine-First" 本质 |
| **B 轴 · 系统对比** | 选型决策树 / 安全模型 / 成本优化 / 记忆系统 | OpenClaw ↔ Hermes 各维度权衡 |
| **C 轴 · 协议基础** | MCP 协议 / ReAct Loop / FTS5 检索原理 | 跨框架通用底层知识 |
| **D 轴 · 实战专题** | 42 Use Cases / 持久 Agent / 定时自动化 / 安全凭证 | 即学即用的工程模式 |

---

## Core Concepts（核心概念）

### 🔬 Hermes 深度原理（A 轴）

|- [[hermes-agent-source-code-architecture|Hermes 源码架构]] — AIAgent 类、工具注册、GEPA 循环、消息循环
|- [[hermes-agent-learning-loop|GEPA 闭环学习系统]] — 宏观层（Goal→Evaluation→Plan→Action）+ 微观层（Gather→Execute→Process→Assess）
|- [[hermes-agent-message-loop|消息循环深度解析]] — 同步循环设计哲学、`toolset__tool_name` 命名空间、并行依赖问题、四个回调机制
|- [[hermes-agent-skills-system|技能系统机制]] — 技能本质、生命周期、触发条件（工具调用 ≥ 5 次）、Curator 维护 [[★ 与 skill-library 的区别：本文讲机制原理，skill-library 讲官方库生态]]
|- [[hermes-agent-skill-library|官方技能库生态]] — 648 技能（77 内置 + 50 官方 + 521 社区）、agentskills.io 开放标准、三级渐进式加载 [[★ 与 skills-system 的区别：本文讲官方生态和使用命令，skills-system 讲底层机制]]

### 🧠 Hermes 配置与集成

|- [[hermes-agent-memory-architecture|四层记忆架构]] — Working → Episodic → MEMORY.md → USER.md；FTS5 检索 [[→ 对比：[[memory-systems-comparison|记忆系统对比]]]]
|- [[hermes-agent-mcp-integration|MCP 集成最佳实践]] — 白名单局限、分层防御、最小暴露原则 [[→ 关联：[[mcp-protocol-deep-dive|MCP 协议深度]]]]
|- [[hermes-agent-soul-agents-md|SOUL.md / AGENTS.md 深度定制]] — 人格定义、权限控制、MD 文件注入体系
|- [[hermes-agent-profiles-multi-instance|Profiles 多实例管理]] — HERMES_HOME 隔离、Kanban 多 Agent 协作
|- [[hermes-agent-voice-mode|Voice Mode]] — TTS/ASR 配置、Telegram 语音通话
|- [[hermes-agent-troubleshooting|排错指南]] — 10 类报错全覆盖、hermes doctor 诊断三步法

### 🆚 OpenClaw ↔ Hermes 对比（B 轴核心）

|- [[openclaw-hermes-decision-tree|选型决策树]] — 三条分叉轴（安全 vs 灵活 / 可见性 vs 自动化 / 技能人主 vs 机主）、混合架构（OpenClaw 网关 + Hermes 执行引擎）[[★ 选型必读]]
|- [[openclaw-hermes-comparison|深度对比分析]] — 356k vs 74k Stars、网关模式 vs 引擎模式、社区生态对比
|- [[openclaw-gateway-architecture|OpenClaw Gateway 架构]] — 纯路由层（不产生智能）、三层架构（渠道接入 → 网关 → 运行时）、WebSocket 实时双向 [[→ 关联：[[openclaw-source-code-architecture|OpenClaw 源码架构]]]]
|- [[memory-systems-comparison|记忆系统对比]] — 7 个维度：运行时状态 vs 文件系统状态、整轮提交 vs append-only、FTS5 vs SQLite-vec [[★ 记忆必读]]
|- [[security-model-comparison|安全模型对比]] — fail-closed vs default-allow+callback、审批时机差异、n8n Proxy Pattern [[★ 安全必读]]
|- [[cost-optimization-comparison|成本优化对比]] — Hermes 优化 token 量 vs OpenClaw 优化缓存命中率、Auxiliary Models 节省 40-60%
|- [[multi-agent-collaboration|多 Agent 协作对比]] — CEO Pattern vs Kanban Orchestrator、共享竞态问题、凭证隔离模式 [[★ 协作必读]]

### 🔧 OpenClaw 核心机制

|- [[openclaw-source-code-architecture|OpenClaw 源码架构]] — Core/Memory/Heartbeat/Session 四大机制深度解析
|- [[openclaw-2026-background-tasks-flows|OpenClaw 后台任务与 Flows 系统]] — 任务感知与恢复 [[→ 深度：[[openclaw-flows-cli|OpenClaw Flows CLI]]]] [[★ 后台任务必读]]
|- [[openclaw-flows-cli|Flows CLI 任务编排]] — 线性设计哲学（有意选择）、SQLite 分类账、可见性 vs 自动完成 [[→ 上游：[[openclaw-2026-background-tasks-flows|OpenClaw 后台任务与 Flows 系统]]]]
|- [[openclaw-2026-plugin-sdk|Plugin SDK 重构]] — extension-api 废弃、plugin-sdk 启用、Context Engine（2026.3）
|- [[openclaw-clawhub|ClawHub 技能生态]] — 13000+ 技能、中国镜像、CLI 命令 [[→ 关联：[[hermes-agent-skill-library|Hermes 技能库]]]]

### 📈 版本演进

|- [[openclaw-v2026-4-5-release|OpenClaw v2026.4.5]] — 多媒体版：图片/音频进入核心、/dreaming、Prompt Cache 优化
|- [[openclaw-v2026-3-11-release|OpenClaw v2026.3.11]] — 安全强化版：WebSocket 源验证、插件隔离、session 沙盒
|- [[hermes-agent-v0-12-release|Hermes v0.12.0]] — 斜杠指令完整化、多会话并行、版本演进时间线

### 🌐 跨框架通用概念

|- [[mcp-protocol-deep-dive|MCP 协议深度理解]] — "USB 标准"类比、白名单局限、分层防御 [[→ 关联：[[hermes-agent-mcp-integration|MCP 集成]]]]
|- [[react-loop-paradigm|ReAct Loop 范式]] — 推理+执行交替、两系统实现差异（显式 vs 隐式）、依赖链不稳定局限 [[→ 关联：[[hermes-agent-message-loop|消息循环]]]]
|- [[fts5-full-text-search|FTS5 全文检索原理]] — BM25 算法、语义局限、session_search 补偿机制 [[→ 关联：[[hermes-agent-memory-architecture|记忆架构]]]]
|- [[persistent-agent-patterns|持久 Agent 核心模式]] — Heartbeat 驱动、自愈、多 Agent 协调 [[→ 关联：[[scheduled-automation|定时自动化]]]]
|- [[scheduled-automation|定时自动化]] — Cron 设计、Morning Briefing、Digest 模式 [[→ 关联：[[persistent-agent-patterns|持久 Agent]]]]
|- [[memory-knowledge-systems|记忆与知识系统]] — Markdown Memory、语义搜索、RAG 知识库 [[→ 系列：[[hermes-agent-memory-architecture|Hermes 记忆]] + [[memory-systems-comparison|对比]]]]
|- [[alert-center-patterns|Alert Center 模式]] — LLM 告警归并、根因分析、团队路由

### 🛡️ 安全与凭证

|- [[security-credential-management|安全凭证管理]] — TruffleHog、API Key 硬编码风险、凭证隔离 [[→ 系列：[[security-model-comparison|安全模型对比]]]]

---

## Use Cases（42 个真实案例）

**分类导航** — 全部案例见 [[usecases/index|Use Cases Index]]

|| 分类 | 说明 ||
||------|------|------|
|| [[usecases/index#multi-agent-systems|Multi-Agent Systems]] | 多 Agent 团队协作 |
|| [[usecases/index#scheduled-automation|Scheduled & Automation]] | 定时任务、Morning Briefing |
|| [[usecases/index#knowledge-memory|Knowledge & Memory]] | 知识库、Second Brain、语义搜索 |
|| [[usecases/index#content-pipelines|Content Pipelines]] | YouTube/X/播客内容流水线 |
|| [[usecases/index#personal-crm|Personal CRM]] | 本地 CRM、电话通知、客户管理 |
|| [[usecases/index#project-management|Project Management]] | 任务管理、状态追踪 |
|| [[usecases/index#automation-infra|Automation & Infra]] | n8n 工作流、自愈服务器 |
|| [[usecases/index#finance|Finance & Tracking]] | 收益追踪、Polymarket 自动交易 |
|| [[usecases/index#research-discovery|Research]] | arXiv 论文、HuggingFace 搜索 |
|| [[usecases/index#life-personal|Life & Personal]] | 家庭日历、健康追踪 |
|| [[usecases/index#assistant|General Assistants]] | 多渠道助手、桌面协作 |
|| [[usecases/index#development|Development]] | 游戏开发、LaTeX 写作 |

---

## Summaries（摘要）

|- [[openclaw-cookbook-summary|OpenClaw Cookbook 核心要点]] — 10 章节精华速查

---

## Raw Sources（Layer 1 · 原始素材）

|| 文件 | 说明 ||
||------|------|
|| `raw/articles/openclaw-cookbook-2026-02.md` | OpenClaw Cookbook 主文档 |
|| `raw/articles/hermes-agent-overview-2026.md` | Hermes Agent 官方概览 |
|| `raw/articles/hermes-agent-use-cases-2026.md` | Hermes Agent 官方 User Stories（99 个案例） |
|| `raw/articles/` | 42 个 use case 原始文章 |

---

## Wiki Structure

本 Wiki 遵循 Karpathy LLM Wiki 的三层架构：

|| Layer | 内容 | 说明 ||
||-------|------|------|------|
|| Layer 1 | `raw/` | 原始来源，immutable ||
|| Layer 2 | `entities/`、`concepts/`、`usecases/`、`summaries/` | Agent 维护的 wiki 页面 ||
|| Layer 3 | `SCHEMA.md` | 领域定义和命名规范 ||

详见：[[SCHEMA|Wiki Schema]]

---

*Last updated: 2026-05-11 | Total pages: 76*
