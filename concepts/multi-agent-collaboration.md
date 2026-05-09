---
title: 多 Agent 协作模式对比
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [openclaw, hermes, multi-agent, orchestration, collaboration, profiles, kanban]
sources: [concepts/multi-agent-orchestration.md, concepts/hermes-agent-profiles-multi-instance.md, raw/articles/multi-agent-team.md, raw/articles/autonomous-project-management.md]
confidence: high
---

# 多 Agent 协作模式对比

## 一、OpenClaw 的三种协作模式

### 1.1 CEO Pattern（推荐入门）

> "Main session = coordinator ONLY. All execution goes to subagents."

```
用户任务
    ↓
Main session（只做 spawn/route/summarize，0-2 次工具调用）
    ↓ sessions_spawn() / sessions_send()
Sub-agents 并行执行，通过 STATE.yaml 共享状态
```

**关键洞察**：传统 orchestrator 模式会让主 Agent 变成"交通警察"瓶颈。CEO Pattern 要求主 session 不执行只协调，强制避免这个瓶颈。

### 1.2 Specialized Team Pattern

2-4 个专业 Agent，各有角色/个性/优化的模型，通过 Telegram group 控制：

```
Milo（战略Lead，Claude Opus）→ @milo
Josh（商业分析，Sonnet）→ @josh
Marketing → @marketing
Dev → @dev
```

起步建议：从 2 个开始（lead + 1 specialist），发现瓶颈再添加。

### 1.3 Chained Pipeline Pattern

Agent 输出依次输入下一个 Agent，全自动化定时运行：

```
Research Agent → Writing Agent → Thumbnail Agent
```

---

## 二、Hermes 的多 Agent 协作体系

### 2.1 Profile 隔离（基础层）

每个 Profile = 完全独立的 `HERMES_HOME`，拥有自己的配置/记忆/会话/技能/网关。

```
~/.hermes/profiles/
├── work/       # 工作 Profile
├── dev/        # 开发 Profile
└── research/   # 研究 Profile
```

### 2.2 Kanban Orchestrator 模式

```
┌─────────────────────────────────────┐
│   Hermes Kanban Orchestrator         │
├──────────┬──────────┬───────────────┤
│ Agent-1  │ Agent-2  │ Agent-3       │
│ Research │ Coding   │ Review        │
│ Profile:A│ Profile:B│ Profile:C     │
└──────────┴──────────┴───────────────┘
```

### 2.3 并行实例（官方示范）

> "每天并行运行 12 个 Hermes 实例来开发 Hermes Agent itself。" — @Teknium

---

## 三、两个系统共享的根本性问题：状态共享竞态

**OpenClaw 的竞态问题**：

> "Main session 和 sub-agent 同时编辑同一个文件时，OpenClaw 的 `edit` 工具要求精确 `oldText` 匹配——内容变化后 edit 会静默失败。"

**OpenClaw 的解法**：拆分两个文件

```
AUTONOMOUS.md        — 主 session 独写（goals + open backlog，<50 行）
memory/tasks-log.md  — append-only 日志，sub-agent 只在末尾追加
                       类比 Git commit log，从不重写历史
```

**Hermes 对此没有显式的官方解决方案**。文档提到 `subagent.timeout` 和 `delegation.max_concurrent_children`，但 sub-agent 之间的状态共享机制没有明确说明。

---

## 四、n8n Proxy Pattern（OpenClaw 独有，最被低估）

```
OpenClaw → webhook call（无凭证）→ n8n Workflow（锁定，有 API keys）→ External API
```

把"凭证管理"和"Agent 决策"分离：
- Agent 只负责决策
- 凭证由 n8n 管理，Agent 永远不接触 API key
- **安全价值巨大**：支付/邮件/CRM 等敏感场景

**Hermes MCP 集成虽有白名单模式，但凭证管理机制不明确。**

---

## 五、协作模式对比

| | OpenClaw | Hermes |
|---|---|---|
| **协调模式** | CEO Pattern（主 session 不执行） | Kanban Orchestrator |
| **隔离机制** | SessionBinding + STATE 文件 | Profile（HERMES_HOME 隔离） |
| **状态共享** | AUTONOMOUS.md + append-only tasks-log | 未明确说明 |
| **凭证隔离** | n8n Proxy Pattern | MCP 白名单模式 |
| **并行能力** | sessions_spawn 并行子 Agent | max_concurrent_children 控制 |
| **通信协议** | sessions_spawn/send（格式未文档化） | delegate_task（格式未文档化） |

---

## 六、知识断层清单

1. **Agent 间通信的指令 schema**：spawn/send 的消息格式是什么？有无类型定义？
2. **Hermes Kanban Orchestrator 瓶颈**：Orchestrator 本身是否会变成性能瓶颈？
3. **Hermes append-only 状态共享**：有没有类似 AUTONOMOUS.md / tasks-log.md 的模式？
4. **子 Agent 异常处理**：sub-agent 崩溃后，主 Agent 如何感知和处理？
5. **多 Agent 上下文隔离**：一个 sub-agent 崩溃会不会污染主 Agent 的上下文？
6. **实际扩展上限**：CEO Pattern 最多能支撑多少个子 Agent？

---

## 相关概念

- [[multi-agent-orchestration]] — OpenClaw 多 Agent 编排（CEO Pattern 详解）
- [[hermes-agent-profiles-multi-instance]] — Hermes Profiles 与多实例管理
- [[openclaw-hermes-decision-tree]] — 选型决策树
