---
title: OpenClaw Heartbeat vs Hermes Cron——两种自动化哲学的深度对比
created: 2026-05-13
type: concept
tags: [openclaw, hermes, heartbeat, cron, automation, background-tasks, philosophy]
sources: [concepts/openclaw-flows-cli.md, concepts/scheduled-automation.md, website/docs/user-guide/features/cron.md, agent/curator.py]
confidence: high
---

# OpenClaw Heartbeat vs Hermes Cron——两种自动化哲学的深度对比

## 一、这不是功能对比，而是哲学分歧

很多人把 Heartbeat 和 Cron 当作"定时任务功能的两种实现"来比较，这没有抓到本质。

两者的真正分歧是：

> **OpenClaw：任务跑了我要能看见它、干预它、审计它**
>
> **Hermes：任务跑了它自己完成就行，我等结果**

这是两种完全不同的自动化价值观。

---

## 二、技术实现对比

### 2.1 任务持久化

| | OpenClaw Flows + Heartbeat | Hermes Cron |
|---|---|---|
| **任务状态存储** | SQLite 分类账 | 无持久化 |
| **Agent 重启后恢复** | ✅ 支持，任务从 SQLite 恢复 | ❌ 重启即丢失 |
| **任务中断点** | 完整记录每个步骤 | 无记录 |

**关键问题：** Hermes Cron 任务在执行过程中，如果 Gateway 重启，任务会怎样？

答案是：丢失。Cron 任务运行在"新鲜会话"中（`skip_memory=True`），没有 SQLite 记录。Gateway 重启 → 任务永久消失。

### 2.2 任务可见性

| | OpenClaw | Hermes |
|---|---|---|
| **运行中任务列表** | `flows list` — 实时可见 | ❌ 没有统一界面 |
| **任务详情查看** | `flows show` — 每步状态 | ❌ 黑箱 |
| **取消任务** | `flows cancel` | ❌ `/stop` 只能中断当前会话 |
| **诊断损坏任务** | `openclaw doctor` 自动修复 | ❌ 只能手动处理 |

### 2.3 触发机制

| | OpenClaw Heartbeat | Hermes Cron |
|---|---|---|
| **触发方式** | 定时（`every: 30m`）+ 可选事件 | 纯定时（cron 表达式或自然语言） |
| **目的** | "到点了检查一下有没有事" | "到点了执行这个任务" |
| **执行模式** | 读取 heartbeat-state.json，执行检查/修复 | 启动新鲜会话，执行 prompt |
| **与 Flows 的关系** | Heartbeat 可以触发 Flows | Cron 是独立系统 |

### 2.4 Hermes Cron 的实际执行流程

```
Cron 触发时间到达
    ↓
Gateway 启动新会话（skip_memory=True）
    ↓
加载 prompt + attached skills
    ↓
执行任务
    ↓
交付结果（origin/local/platform）
    ↓
会话关闭，无任何持久状态
```

关键：`skip_memory=True` 意味着每次 cron 运行都是"从零开始"，不继承任何上下文。

---

## 三、OpenClaw Heartbeat 的实际执行流程

```
Heartbeat 定时触发
    ↓
读取 heartbeat-state.json（上次状态）
    ↓
执行检查：哪些任务需要继续？哪些服务挂了？
    ↓
执行修复操作（重启服务、更新配置等）
    ↓
更新 heartbeat-state.json（新状态）
    ↓
Flows 记录任务进度到 SQLite
```

关键：状态是持久的，Heartbeat 的"检查"是基于上次状态的增量操作。

---

## 四、两种设计选择的后果

### 4.1 Hermes 的选择：简单可靠

**优点：**

- 实现简单，不依赖复杂的任务状态管理
- 每个任务都是"干净"的新会话，不会有状态污染
- Skills + prompt 的组合足够强大，可以描述几乎任何任务

**代价：**

- 任务执行过程完全不透明
- Gateway 重启会丢失正在运行的任务
- 没有办法"查看任务跑到哪了"
- 没有办法在任务中途干预

### 4.2 OpenClaw 的选择：可见可控

**优点：**

- 任务完整生命周期可审计
- Agent 重启后任务自动恢复
- `openclaw doctor` 可以自动修复损坏的任务
- 用户对正在运行的任务有完全的控制权

**代价：**

- 实现复杂（SQLite 状态管理、doctor 修复逻辑）
- Flow 的"线性"设计限制了复杂编排的可能性
- 状态管理带来了额外的维护负担

---

## 五、什么时候选哪个

### 5.1 选 Hermes Cron 的场景

- **任务天然幂等**：比如"每天 9 点发日报"，跑两次和跑一次效果一样
- **不需要中途干预**：任务一旦开始，就让它跑到底
- **结果导向**：只要结果对就行，过程不重要
- **快速搭建**：不想花时间配置状态管理和监控

**典型场景：**

- 定时爬取内容 → 摘要 → 发送
- 定时备份数据
- 定时检查服务健康状态（不关心过程，只关心结果）

### 5.2 选 OpenClaw Heartbeat/Flows 的场景

- **任务有中间状态**：中断后需要从断点继续
- **需要随时查看进度**：跑了 30 分钟，想知道现在到哪一步了
- **可能需要人工干预**：任务中途可能需要人做决定
- **需要审计追踪**： Regulatory compliance 或安全审计要求

**典型场景：**

- 长时间数据迁移（有检查点）
- 多步骤部署流程（需要看到每步状态）
- 需要人工审批的工作流

### 5.3 选 OpenClaw 但不用 Heartbeat 的场景

Heartbeat 适合"主动巡检"（检查状态、执行修复），但如果只是"定时跑一个任务"，Flows 本身就能满足需求，不需要 Heartbeat。

---

## 六、融合的可能性

两者其实解决的是不同问题：

- **Heartbeat = 主动监控 + 自动修复**（解决问题）
- **Cron/Flows = 定时任务执行**（执行任务）

理论上最好的架构是：**Hermes 的对话式任务创建（低门槛）+ OpenClaw 的任务持久化和可见性（可靠性）**。

但这需要根本性的架构重构，在实践中两者不太可能融合。

---

## 七、真正的洞见

> **Heartbeat 的本质不是"定时任务"，而是"定时巡检"。**

OpenClaw 的 Heartbeat 解决的是："我怎么知道我的系统有没有问题？"

Hermes 的 Cron 解决的是："我怎么定时执行一个任务？"

这是两个不同的问题域。OpenClaw 把"监控 → 发现 → 修复"的闭环做进了框架里，Hermes 则假设监控和修复是外部系统的事，它只管"执行给定的任务"。

---

## 相关概念

- [[hermes-agent]] — Hermes 整体框架
- [[openclaw-flows-cli]] — OpenClaw Flows CLI 深度解析
- [[scheduled-automation]] — 定时自动化设计模式（OpenClaw 视角）
- [[hermes-agent-learning-loop]] — Hermes 自我进化机制
- [[openclaw-hermes-comparison]] — 两者完整对比
