---
title: Wiki Index
summary: OpenClaw & LLM Agent 学习 Wiki 导航页
tags: [meta]
created: 2026-05-08
updated: 2026-05-08
---

# OpenClaw & LLM Agent Wiki

> 用于 AI 工具学习、个人知识管理、Agent 工作流设计。
> 基于 [awesome-openclaw-usecases](https://github.com/hesamsheikh/awesome-openclaw-usecases) + OpenClaw Cookbook 构建。

---

## Entities

- [[openclaw]] — AI Agent 平台，支持多 Agent、子 Agent、记忆系统和消息平台集成
- [[hermes-agent]] — Nous Research 开源的自我进化 AI Agent 框架，"The agent that grows with you"

---

## Core Concepts

- [[openclaw-2026-background-tasks-flows]] — 后台任务管理 + Flows CLI：SQLite 分类账、任务感知与恢复（2026.3.31）
- [[openclaw-2026-plugin-sdk]] — 插件系统重构：旧 extension-api 废弃、plugin-sdk 启用、Context Engine（2026.3）
- [[multi-agent-orchestration]] — 多 Agent 协作模式：CEO Pattern、Specialized Team、Chained Pipeline
- [[memory-knowledge-systems]] — 记忆与知识系统：Markdown Memory、语义搜索、RAG 知识库
- [[scheduled-automation]] — 定时自动化：Crontab 设计、Morning Briefing、Digest 模式
- [[security-credential-management]] — 安全与凭证管理：TruffleHog、n8n 凭证隔离
- [[persistent-agent-patterns]] — 持久 Agent 核心模式：Heartbeat、自愈、多 Agent 协调
- [[alert-center-patterns]] — LLM Alert Center：告警归并、根因分析、团队路由
- [[hermes-agent-learning-loop]] — Hermes 闭环学习系统：GEPA 引擎驱动的自我进化机制
- [[hermes-agent-memory-architecture]] — Hermes 四层记忆架构：Working → Episodic → MEMORY.md → USER.md
- [[hermes-agent-skills-system]] — Hermes 技能系统：自动沉淀解题模式为可复用 Skill

---

## Use Cases

**42 个真实 OpenClaw 用例**，按场景分类：

| 分类 | 说明 |
|------|------|
| [[usecases/index#multi-agent-systems|Multi-Agent Systems]] | 多 Agent 团队协作 |
| [[usecases/index#scheduled-automation|Scheduled & Automation]] | 定时任务、Morning Briefing |
| [[usecases/index#knowledge-memory|Knowledge & Memory]] | 知识库、Second Brain、语义搜索 |
| [[usecases/index#content-pipelines|Content Pipelines]] | YouTube/X/播客内容流水线 |
| [[usecases/index#personal-crm|Personal CRM]] | 本地 CRM、电话通知、客户管理 |
| [[usecases/index#project-management|Project Management]] | 任务管理、状态追踪 |
| [[usecases/index#automation-infra|Automation & Infra]] | n8n 工作流、自愈服务器 |
| [[usecases/index#finance|Finance & Tracking]] | 收益追踪、Polymarket 自动交易 |
| [[usecases/index#research-discovery|Research]] | arXiv 论文、HuggingFace 搜索 |
| [[usecases/index#life-personal|Life & Personal]] | 家庭日历、健康追踪 |
| [[usecases/index#assistant|General Assistants]] | 多渠道助手、桌面协作 |
| [[usecases/index#development|Development]] | 游戏开发、LaTeX 写作 |

👉 [[usecases/index|浏览全部 42 个 Use Cases →]]

---

## Raw Sources（Layer 1）

原始来源文件，immutable：

- `raw/articles/openclaw-cookbook-2026-02.md` — OpenClaw Cookbook 主文档
- `raw/articles/` — 42 个 use case 原始文章

---

## Summaries

- [[openclaw-cookbook-summary]] — Cookbook 核心要点速查（10 章节精华）

---

## Wiki Structure

本 Wiki 遵循 Karpathy LLM Wiki 的三层架构：

| Layer | 内容 | 说明 |
|-------|------|------|
| Layer 1 | `raw/` | 原始来源，immutable |
| Layer 2 | `entities/`、`concepts/`、`usecases/` | Agent 维护的 wiki 页面 |
| Layer 3 | `SCHEMA.md` | 领域定义和命名规范 |

详见：[[SCHEMA|Wiki Schema]]

---

*Last updated: 2026-05-08*
