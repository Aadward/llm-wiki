---
title: Persistent Agent Patterns
summary: 持久 Agent（持续工作 Agent）的核心架构模式：Heartbeat 调度、状态管理、自动修复、知识累积
tags: [agent, automation, workflow, concept]
sources: [raw/articles/openclaw-cookbook-2026-02.md, raw/articles/self-healing-home-server.md, raw/articles/multi-agent-team.md]
created: 2026-05-08
---

# Persistent Agent Patterns

持久 Agent 的核心特征：**不被动等待命令，而是按时间表主动工作，持续累积上下文。**

## Core Principle: Heartbeat-Driven

```
不是: User asks → Agent responds
而是: Cron tick → Agent checks → acts autonomously
```

持久 Agent 的价值随时间**复合增长** — 越久越懂你，越懂你越有价值。

## Pattern 1: Self-Healing Server

适合：个人/团队基础设施管理

**核心组件：**
- `HEARTBEAT.md` — 调度时间表（分钟/小时/天/周）
- `memory/` — 累积式永久记忆
- SSH/Kubectl/Terraform — 可执行操作

**典型调度：**
```
Every 15min → 检查任务面板，继续工作
Every hour   → 健康检查、邮件分类
Every 6hr    → 知识库录入、自我诊断
Daily 8AM    → Morning Briefing（天气/日历/系统/任务）
Weekly       → 安全审计、知识库 QA
```

**关键洞察：** "Agent 可以在你还没发现问题时，就通过 SSH、kubectl 修复问题。"

## Pattern 2: Multi-Agent Coordination (CEO Pattern)

适合：需要并行处理多个专业领域的场景

**核心原则：**
- Main session = 协调器（只做 spawn/route/summarize，0-2 tool calls）
- Sub-agents = 执行器（各司其职，并行工作）
- 通过 `sessions_spawn` / `sessions_send` 控制

**STATE.yaml Race Condition 解法：**
- `AUTONOMY.md` — 主 session 独写（goals + open backlog，< 50 行）
- `memory/tasks-log.md` — append-only 日志，sub-agent 只追加不编辑

类比 Git commit log：从不重写历史，只追加新提交。

## Pattern 3: Memory Flywheel

持久 Agent 的真正护城河是**知识累积**：

```
每次事件 → LLM 提炼 → 入知识库（向量索引）
下次类似 → LLM 查知识库 → 直接复用诊断路径
```

从"发短信一样简单"的 capture，到"搜索"的 retrieval，零摩擦积累。

## Security is a Prerequisite

> ⚠️ **"AI assistants will happily hardcode secrets."** — 持久 Agent 因为长期运行、权限大，是最高风险场景。

必须配置：
1. **TruffleHog pre-push hooks** — 阻止凭证暴露
2. **n8n webhook 隔离** — Agent 调 webhook，凭证锁在 n8n
3. **Local-first Git** — Agent 不直接 push public repo
4. **Branch protection** — PR required for main

## Comparison: Single vs Multi-Agent

| | Single Persistent Agent | Multi-Agent Team |
|--|----------------------|-----------------|
| 适用场景 | 个人基础设施、简单监控 | 复杂业务、并行多领域 |
| 复杂度 | 低 | 中高 |
| 状态管理 | AUTONOMY.md | 共享文件 + append-only log |
| 起步建议 | Self-Healing Server | 2 agents（lead + 1 specialist）|

## Related

- [[openclaw]] — the platform
- [[scheduled-automation]] — cron patterns and Morning Briefing
- [[multi-agent-orchestration]] — multi-agent coordination details
- [[memory-knowledge-systems]] — memory layers and RAG
- [[security-credential-management]] — security must-haves
