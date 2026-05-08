---
title: Multi-Agent Orchestration
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [agent, workflow]
sources: [raw/articles/openclaw-cookbook-2026-02.md]
confidence: high
---

# Multi-Agent Orchestration

多 Agent 协作模式 — 如何让多个 AI Agent 协同工作而非单打独斗。

## Core Problem

单一 Agent 上下文窗口有限，无法同时处理战略、代码、营销研究、商业分析等多个领域。让专业 Agent 各司其职，通过协调机制协作，效率远超单体 Agent。

## Patterns

### CEO Pattern（推荐入门）

Main session 保持精简 — 只负责 spawn、route、summarize。所有执行交给子 Agent。

```text
## AGENTS.md — CEO Delegation Pattern

Main session = coordinator ONLY. All execution goes to subagents.

Workflow:
1. New task arrives → check PROJECT_REGISTRY.md for existing PM
2. If PM exists → sessions_send(label="pm-xxx", message="[task]")
3. If new project → sessions_spawn(label="pm-xxx", task="[task]")
4. PM executes, updates STATE.yaml, reports back
5. Main agent summarizes to user

Rules:
- Main session: 0-2 tool calls max (spawn/send only)
- PMs own their STATE.yaml files
- PMs can spawn sub-sub-agents for parallel subtasks
- All state changes committed to git
```

**优势：** 主 session 响应快，子 Agent 并行执行，git 版本控制可审计。

### Specialized Team Pattern

2-4 个专业 Agent，各有角色、个性、优化的模型，通过一个 Telegram group 控制。

| Agent | 角色 | 模型 | 每日任务 |
|-------|------|------|----------|
| Milo | Strategy Lead | Claude Opus | 8AM standup, 6PM recap |
| Josh | Business Analyst | Claude Sonnet | 9AM metrics pull |
| Marketing | Content Researcher | Gemini | 10AM content ideas |
| Dev | Coding Agent | Claude Opus/Codex | CI/CD health, PR review |

**起步建议：** 从 2 个开始（lead + 1 specialist），发现瓶颈再添加。

### Chained Pipeline Pattern

链式 Pipeline，Agent 输出依次输入下一个 Agent。全自动化，定时运行。

```
Research Agent (#research) → Writing Agent (#scripts) → Thumbnail Agent (#thumbnails)
```

## Critical: STATE.yaml Race Condition

**问题：** Main session 和 sub-agent 同时编辑同一个文件时，OpenClaw 的 `edit` 工具要求精确 `oldText` 匹配 — 内容变化后 edit 会静默失败。

**解法：拆分为两个文件**

1. **`AUTONOMOUS.md`** — 主 session 独写，保留 goals + open backlog。保持 <50 行（每次 heartbeat 都加载）。
2. **`memory/tasks-log.md`** — append-only 日志。Sub-agent 只在末尾**追加新行**，绝不编辑已有内容。

```markdown
# tasks-log.md — Completed Tasks (append-only)
# Sub-agents: always append to the END. Never edit existing lines.

### 2026-02-24
- ✅ TASK-001: Research competitors → research/competitors.md
- ✅ TASK-002: Draft blog post → drafts/post-1.md
```

类比 Git commit log — 从不重写历史，只追加新提交。

## n8n Proxy Pattern（凭证隔离）

让 Agent 通过 n8n webhook 代理所有外部 API 调用，凭证永不离开 n8n。

```
OpenClaw → webhook call (no creds) → n8n Workflow (locked, with API keys) → External API
```

流程：
1. Agent 设计并通过 API 创建 n8n workflow（含 webhook trigger）
2. 人工在 n8n UI 添加凭证
3. **人工锁定 workflow**（防止 Agent 修改）
4. Agent 调用 webhook URL + JSON payload，从不接触 API key

## Related

- [[openclaw]] — the platform this pattern runs on
- [[scheduled-automation]] — cron jobs that power multi-agent workflows
- [[security-credential-management]] — n8n isolation and credential best practices