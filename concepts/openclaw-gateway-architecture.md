---
title: OpenClaw Gateway 架构深度解析
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [openclaw, gateway, architecture, session, websocket, background-tasks, flows]
sources: [concepts/openclaw-source-code-architecture.md, concepts/openclaw-2026-background-tasks-flows.md, concepts/openclaw-v2026-4-5-release.md]
confidence: high
---

# OpenClaw Gateway 架构深度解析

## 一、Gateway 的本质：一个纯路由层

> "Gateway 是整个系统的**神经中枢**，**不产生智能**，只负责：消息路由、会话管理、任务协调、安全审批、记忆管理。"

这句话揭示了 OpenClaw 与 Hermes 的**根本性架构差异**：

| | Hermes | OpenClaw |
|---|---|---|
| **智能所在** | AIAgent Core 本身即智能 | Core 只是执行器，Gateway 不产生智能 |
| **设计哲学** | Engine-First（引擎即大脑） | Gateway-First（网关即神经中枢） |
| **LLM 调用位置** | AIAgent Core 内 | Layer 3 Core 内 |
| **transport 差异** | CLI/Gateway 共享同一 AIAgent Core | Gateway 和 Core 是不同层次 |

**核心含义**：OpenClaw 中换一个 LLM 或推理服务，只需修改 Layer 3 Core，无需触碰 Gateway。Gateway 专注"连接人"，Core 专注"执行 AI"。

---

## 二、三层架构详解

```
┌──────────────────────────────────────────────────────┐
│  Layer 1: 渠道接入（Channel）                          │
│  WhatsApp / Telegram / Slack / 飞书 / Discord ...     │
└──────────────────────┬───────────────────────────────┘
                       ↓
┌──────────────────────────────────────────────────────┐
│  Layer 2: Gateway（网关层）— 神经中枢                    │
│                                                      │
│  · 消息路由：判断消息来源和目标                         │
│  · 会话管理：每个对话独立上下文                         │
│  · 任务协调：AI、工具、频道之间的交互                   │
│  · 安全审批：危险操作拦截                             │
│  · 记忆管理：调用 Memory Manager                       │
│  · 主要通信：WebSocket（实时双向）                    │
└──────────────────────┬───────────────────────────────┘
                       ↓
┌──────────────────────────────────────────────────────┐
│  Layer 3: Agent Runtime（运行时）                       │
│  Model（模型）+ Skills（技能）+ Memory（记忆）           │
│  Core/agent — Agent 主逻辑                            │
│  Core/planner — 任务规划                               │
│  Core/executor — 执行器                               │
│  Core/skill_manager — 技能管理                        │
│  Core/memory_manager — 记忆管理                        │
│  Core/runtime_loop — 运行时循环（ReAct Loop）          │
└──────────────────────────────────────────────────────┘
```

---

## 三、WebSocket 优先的设计意图

Gateway 主要走 WebSocket，这不是单纯的技术选型，而是架构假设的体现：

1. **消息平台天然模型**：Telegram/Discord/WhatsApp 本身支持 WebSocket 或长轮询，Gateway 需要对接推送机制
2. **流式响应**：LLM streaming 响应需要实时推送，HTTP 无法主动服务端推送
3. **会话状态维护**：WebSocket 保持连接，天然适合维护 session_id → context 映射

**与 Hermes 对比**：Hermes CLI 是轮询式的，Gateway 是 adapter 模式，不强调 WebSocket 优先。这导致 OpenClaw 更适合实时聊天/多人协作场景，Hermes 更适合定时任务/低频触发场景。

---

## 四、八大 MD 文件注入体系

每次会话启动时，Gateway 自动注入 8 个文件为 System Prompt：

| 文件 | 职责 | Hermes 对应 |
|------|------|------------|
| `SOUL.md` | 人格：说话风格、态度、行为边界 | SOUL.md（同名） |
| `AGENTS.md` | Agent 行为指令 | AGENTS.md（同名） |
| `USER.md` | 用户画像 | USER.md（同名） |
| `TOOLS.md` | 可用工具列表（静态） | 动态生成（`_get_skills_index()`） |
| `IDENTITY.md` | 身份定义 | 无显式对应 |
| `HEARTBEAT.md` | 心跳配置 | cron 系统 |
| `MEMORY.md` | 主记忆 | MEMORY.md（同名） |
| `BOOTSTRAP.md` | 启动引导 | 无显式对应 |

**关键差异**：OpenClaw 的工具列表**静态化**为 TOOLS.md 文件，LLM 只能看到文件内容，更可预测但更僵化。Hermes 的工具列表**动态生成**，可根据上下文调整可见工具集，但依赖模型正确解读 skills index。

---

## 五、Session 绑定与会话特性

`SessionBindingService` 的核心职责：

- 将"任务/子代理"与物理"对话"绑定
- 整个生命周期内追踪和管理会话
- 支持跨会话上下文共享

**会话特性**：

| 特性 | 说明 |
|------|------|
| **独立性** | 每个对话独立会话，上下文隔离 |
| **连续性** | 通过 .md 文件实现跨会话记忆 |
| **并发性** | 支持多会话并发处理 |

**JSONL append-only 日志**（sessions/store.ts）：
- 新事件追加到日志文件
- 定期压缩旧日志
- 支持多会话并发读写
- 异常情况下可恢复

---

## 六、后台任务与 Flows CLI（OpenClaw 最被低估的能力）

### SQLite 分类账：任务感知和恢复

```
任务创建 → 后台运行 → SQLite 记录 → 任务感知 → 丢失恢复
                                              ↑
                                    Agent 重启后可继续执行
```

关键优势：Hermes 的 cronjob 是"触发后即忘记"，Agent 重启后任务丢失。OpenClaw 的 SQLite 分类账记录完整生命周期，重启后可查询并继续。

### Flows CLI：任务可见性

```
openclaw flows list    # 列出所有运行中的流
openclaw flows show    # 查看特定流详情
openclaw flows cancel  # 取消指定流
```

**这是 Hermes 完全缺失的能力**。Hermes 的定时任务没有统一监控界面，任务状态几乎不透明。OpenClaw 把"任务可见性"当作核心工程能力来建设。

### 统一的后台任务控制平面

2026.3.31 将 ACP、子代理、cron 和后台 CLI 统一到同一 SQLite 分类账：
- 统一的生命周期管理
- 审计/维护/状态可见性改进
- 任务丢失自动恢复

---

## 七、安全模型深度对比

| 维度 | OpenClaw | Hermes |
|------|----------|--------|
| **令牌管理** | trusted-proxy 拒绝混合令牌，令牌轮换立即断开 session | approval_callback 运行时审批 |
| **节点命令** | 配对后默认禁用，需显式批准 | sudo_callback（机制未知） |
| **环境隔离** | 阻止请求范围 env var 覆盖 | 无明确对应设计 |
| **技能安装** | critical 危险代码默认失败扫描 | 无对应扫描机制 |
| **安全哲学** | **默认拒绝**（fail-closed） | **默认允许但可干预**（callback） |

OpenClaw 面向企业级严格权限控制，Hermes 面向个人灵活使用。

---

## 八、架构哲学的核心对立

```
OpenClaw:  渠道 → [Gateway 路由 + 记忆管理 + 安全审批] → Core(模型+技能+内存)
           "我提供场地，你来思考"

Hermes:    渠道 → [AIAgent 核心引擎] ← [记忆 + GEPA + 工具注册]
           "我来思考，你来触达"
```

这解释了所有使用场景的差异：
- OpenClaw 更适合**多用户、多渠道、需要严格安全审计**的企业场景
- Hermes 更适合**个人深度定制、长期记忆进化、自动进化**的使用场景

---

## 知识断层清单

1. **WebSocket 重连机制**：连接断开后，session 状态如何恢复？心跳间隔？
2. **SessionBindingService 绑定协议**：任务/子代理与会话绑定的具体数据结构
3. **MD 文件注入优先级**：TOOLS.md/IDENTITY.md 与 AGENTS.md 的优先级关系
4. **Flows 是否支持并行分支**：还是纯线性任务流？
5. **ACP 协议含义**："Shared Background Run Control Plane"中 ACP 的协议格式
6. **心跳与 Flows 的关系**：Heartbeat 任务是否也写入 SQLite 分类账？

---

## 相关概念

- [[openclaw-source-code-architecture]] — 源码架构全文
- [[openclaw-2026-background-tasks-flows]] — 后台任务与 Flows 系统
- [[openclaw-v2026-4-5-release]] — v2026.4.5 版本发布
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比分析
