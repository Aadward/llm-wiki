---
title: OpenClaw 源码架构深度解析
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [openclaw, source-code, architecture, memory, heartbeat, session]
sources: [web/cloud.tencent.com/developer/article/2662888, web/cloud.tencent.com/developer/article/2650019, web/blog.csdn.net/Darlingqiang/article/details/158770236]
confidence: high
---

# OpenClaw 源码架构深度解析

## 概述

OpenClaw（原名 Clawdbot/Moltbot）是由 Peter Steinberger 创建的开源 AI Agent 框架，定位是"**The AI that actually does things**"。其架构设计强调**本地优先**、**纯 Markdown 文件为单一源真理**，通过 Session 会话机制、Memory 记忆系统和 Heartbeat 心跳机制实现跨会话状态管理。

---

## 一、三层核心架构

```
┌─────────────────────────────────────────────────────────┐
│              第一层：渠道接入（Channel）                    │
│         WhatsApp / Telegram / Slack / 飞书                │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│              第二层：Gateway（网关层）—— 神经中枢           │
│    消息路由 · 会话管理 · 任务协调 · 安全审批 · 记忆管理      │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│              第三层：Agent Runtime（运行时）—— 思考核心     │
│         Model（模型）+ Skills（技能）+ Memory（记忆）       │
└─────────────────────────────────────────────────────────┘
```

---

## 二、Core（核心运行时）

### 2.1 Core 职责

Core = Agent 的核心运行逻辑，负责：
- 接收任务
- 调用模型
- 调度 Skills
- 管理 Memory
- 控制 Agent 生命周期

### 2.2 执行流程（ReAct Loop）

```
user input → plan → reason → call skill → observe → update memory
```

### 2.3 Core 核心结构

```
core/
├── agent           # Agent 主逻辑
├── planner         # 任务规划
├── executor        # 执行器
├── skill_manager   # 技能管理
├── memory_manager  # 记忆管理
└── runtime_loop    # 运行时循环
```

---

## 三、Memory（记忆系统）

### 3.1 设计原则

OpenClaw 的 Memory 系统遵循：
- **一切记忆以纯 Markdown 文件为单一源真理（Source of Truth）**
- 模型仅读取磁盘上显式写入的内容
- 会话状态通过 **JSONL append-only 日志 + 自动 compaction** 管理
- 检索层采用 **SQLite-vec 驱动的 hybrid 搜索**（vector + BM25 + MMR + temporal decay）

### 3.2 核心文件

| 文件 | 作用 |
|------|------|
| `MEMORY.md` | 主记忆文件 |
| `memory/*.md` | 分段记忆目录 |
| `sessions/store.ts` | 会话存储引擎（TypeScript） |

### 3.3 记忆读写流程

```
1. 会话启动 → 读取 MEMORY.md + memory/*.md
2. 涉及历史时 → 语义搜索记忆文件
3. 重要信息 → 写入 memory/ 目录持久化
4. 会话结束 → 记忆保留在文件中
```

### 3.4 sessions/store.ts 核心机制

`sessions/store.ts` 是 OpenClaw 的会话存储引擎：
- **append-only 日志**：新事件追加到日志文件
- **自动 compaction**：定期压缩旧日志
- **并发控制**：支持多会话并发读写
- **容错处理**：异常情况下的数据恢复

---

## 四、Heartbeat（心跳机制）

### 4.1 概念

**Heartbeat = 定时唤醒 AI Agent**。OpenClaw 按照设定的时间间隔自动让 Agent 执行一次检查。

### 4.2 核心文件

| 文件 | 作用 |
|------|------|
| `HEARTBEAT.md` | 定义巡检任务清单 |
| `heartbeat-state.json` | 状态持久化 |

### 4.3 配置格式

```yaml
# HEARTBEAT.md
every: 30m    # 间隔 30 分钟执行一次

# 支持 cron 表达式
every: "0 9 * * *"  # 每天 9:00 执行
```

### 4.4 执行流程（5 步闭环）

```
1. 调度触发（Scheduler）
   后台守护进程按配置周期触发心跳
   支持 cron 精确时间调度与 wakeMode: now/next-heartbeat 立即唤醒

2. 读取配置
   读取 HEARTBEAT.md

3. 执行任务
   执行内置提示词：
   "read heartbeat.md if it exists. follow it strictly.
    if nothing needs attention, reply heartbeat_ok."

4. 响应过滤（静默机制）
   - 正常（HEARTBEAT_OK）→ 系统静默丢弃，不通知用户
   - 异常/有任务 → 转发给用户或指定渠道

5. 状态更新
   更新 heartbeat-state.json
```

### 4.5 wakeMode 选项

| 模式 | 说明 |
|------|------|
| `now` | 立即唤醒执行 |
| `next-heartbeat` | 等到下次心跳时间执行 |
| 默认 | 按配置的 `every` 间隔执行 |

---

## 五、会话机制（Session）

### 5.1 Session 会话绑定

OpenClaw 的 `SessionBindingService` 负责：
- 将逻辑上的"任务"或"子代理"与物理的"对话"绑定
- 在整个生命周期内追踪和管理会话
- 支持跨会话的上下文共享

### 5.2 会话特性

- **独立性**：每个对话是独立会话，上下文互相隔离
- **连续性**：通过 .md 文件实现跨会话记忆
- **并发性**：支持多会话并发处理

### 5.3 八大 MD 文件注入

每次会话启动时，Gateway 自动将以下文件注入为 System Prompt：

| 文件 | 作用 |
|------|------|
| `SOUL.md` | 灵魂/人格定义 — 决定说话风格、态度、行为边界 |
| `AGENTS.md` | Agent 行为指令 |
| `USER.md` | 用户画像 |
| `TOOLS.md` | 可用工具列表 |
| `IDENTITY.md` | 身份定义 |
| `HEARTBEAT.md` | 心跳配置 |
| `MEMORY.md` | 主记忆 |
| `BOOTSTRAP.md` | 启动引导 |

---

## 六、Gateway（网关层）

### 6.1 Gateway 职责

Gateway 是整个系统的**神经中枢**，不产生智能，只负责：
- **消息路由**：判断消息来源和目标
- **会话管理**：每个对话独立上下文
- **任务协调**：协调 AI、工具、频道之间的交互
- **安全审批**：危险操作拦截
- **记忆管理**：调用 Memory Manager

### 6.2 通信方式

主要走 **WebSocket**，保证实时双向数据流转。

---

## 七、与 Hermes Agent 对比

| 维度 | OpenClaw | Hermes Agent |
|------|----------|--------------|
| **记忆机制** | 纯 Markdown 文件 | 四层记忆架构（SQLite + FTS5） |
| **会话模型** | JSONL append-only 日志 | 持久化会话 + 情景记忆 |
| **心跳机制** | HEARTBEAT.md + 心跳守护进程 | Cron 定时任务 |
| **技能系统** | 基于文件的 Skills | 三级渐进式 Skills |
| **主动性** | Heartbeat 定时唤醒 | GEPA 闭环学习 |

---

## 相关概念

- [[openclaw]] — OpenClaw 整体介绍
- [[openclaw-2026-plugin-sdk]] — 2026 Plugin SDK 重构
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比
