---
title: Hermes Agent 失败模式与不适用场景
created: 2026-05-10
type: concept
tags: [hermes, failure-modes, anti-patterns, limitations, pitfalls, reliability]
sources: [concepts/hermes-agent-message-loop.md, concepts/hermes-agent-best-practices.md, concepts/hermes-agent-memory-architecture.md]
confidence: high
---

# Hermes Agent 失败模式与不适用场景

## 阅读说明

本文档假设你已经了解 Hermes 的基本工作机制。它的价值在于告诉你**什么情况下 Hermes 会出问题、为什么出问题、如何规避**，而不是重复介绍功能。

---

## 一、最核心的失败模式：整轮提交（Whole-Turn Memory Commit）

### 问题描述

Hermes 在对话结束时才调用 `_save_to_memory()`，整个对话只保存一次。

```python
# Step 6: 整个对话只保存一次
if not self.skip_memory:
    self._save_to_memory(user_message, final_response)
```

### 后果

| 场景 | 结果 |
|------|------|
| 对话中途崩溃 | 所有中间过程丢失 |
| 50 轮对话中出现问题 | 无法回退到前 30 轮 |
| `/undo` | 只撤销模型最后一条回复，无法撤销整个会话 |

### 根本原因

这与 OpenClaw 的 append-only JSONL 日志（每次工具调用都追加）形成鲜明对比。OpenClaw 最多丢失最后一次工具调用的结果，Hermes 可能丢失整个会话。

### 适用场景判断

**如果你需要中间过程可审计：不要用 Hermes。**

典型场景：
- 监管合规要求（regulatory compliance）需要记录每个决策步骤
- 需要回放某个会话的全部过程
- 长时间运行的任务需要中间断点

---

## 二、并行工具调用的隐含依赖陷阱

### 问题描述

当模型生成多个 `tool_calls` 时，它们是**并行分发**的：

```
assistant: tool_calls = [
    {id: "call_A", function: {name: "web__search", ...}},
    {id: "call_B", function: {name: "terminal__run", ...}}
]
    ↓ 并行分发
tool: {tool_call_id: "call_A", content: "..."}
tool: {tool_call_id: "call_B", content: "..."}   ← 无顺序保证
```

### 致命场景

如果 B 依赖 A 的结果（如 A 获取代码仓库地址，B 在该地址执行 `git clone`），当前架构**没有显式依赖声明机制**。B 可能在 A 返回前就执行，导致失败。

### 现状

模型需要自己理解"先 X 后 Y"的隐含依赖，并在 `tool_calls` 序列中正确排序。这是脆弱的设计，依赖模型的隐式推理能力。

### 规避方式

- 复杂依赖链拆成多个单工具调用
- 不要在一次响应中让两个工具有隐含依赖

---

## 三、Skills Index 通货膨胀（Token Cost 陷阱）

### 问题描述

每次 `_send_to_model()` 都会注入 Skills Index（约 20 tokens × skill 数量）：

| 技能数量 | Skills Index tokens | 每次 API 调用额外成本 |
|----------|---------------------|---------------------|
| 10 | ~200 | 可忽略 |
| 50 | ~1,000 | 显著 |
| 200 | ~4,000 | 非常显著 |
| 500+ | ~10,000 | 可能触发 context 警告 |

### 根本矛盾

"越用越聪明"的代价是：技能库越大，每次对话的 token 消耗越高。

### Curator 的缓解作用

Curator 会主动将窄范围 Skill 合并为伞形 Skill，但：
1. Curator 的合并策略是保守的，不会主动拆分
2. 如果 Skill 描述越来越详细，Index 成本仍会增长
3. 没有自动压缩 Index 的机制

### 适用场景判断

**如果你预期 Skill 库会快速扩张到 200+，提前关注 curator 维护策略。**

---

## 四、Cron 任务无状态持久化

### 问题描述

Hermes Cron 任务的实际执行流程：

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

### 后果

- **Gateway 重启 → 任务永久消失**
- 没有任务列表界面（无法查看"有哪些定时任务在跑"）
- 无法取消已开始的任务（`/stop` 只能中断当前会话）
- 任务执行过程完全不透明

### 适用场景判断

**不适合任何需要中途查看进度或干预的任务。**

适合的场景：
- 任务天然幂等（"每天 9 点发日报"，跑两次和跑一次效果一样）
- 不需要中途干预（任务一旦开始就跑到底）
- 结果导向（只要结果对，过程不重要）

**不适合的场景：**
- 多步骤部署流程（需要看到每步状态）
- 需要人工审批的工作流
- 长时间运行的数据迁移（有检查点需求）

---

## 五、回调机制的黑盒问题

### 问题描述

Hermes 有四个回调机制，但三个完全缺乏文档：

| 回调 | 用途 | 文档透明度 |
|------|------|-----------|
| `clarify_callback` | 模型不确定时向用户确认 | 最低（仅参数名） |
| `approval_callback` | 危险操作需用户批准 | 少量（超时自动拒绝） |
| `sudo_callback` | 提升权限执行管理操作 | **无** |
| `progress_callback` | 报告任务进度 | **无** |

### 实际风险

- 不知道回调的函数签名
- 不知道触发时机
- 不知道返回值如何影响后续流程
- 高级集成（自定义审批流程）几乎无法实现

### 缓解方式

在官方文档完善之前，如果需要确定性行为，回调相关的功能**不要依赖**，改用显式的工具调用模式替代。

---

## 六、Context Compressor 的效果无数据支撑

### 问题描述

Hermes 的 Context Compressor 在上下文快满时触发，但：
- 压缩后保留多少信息，没有量化数据
- 摘要质量如何，没有评测标准
- 压缩后的上下文是否仍然能正确回答问题，未知

### 风险

对于需要精确上下文的复杂推理任务，如果 Context Compressor 误删关键信息，模型可能产生幻觉。

---

## 七、Prompt Caching 与 Skills Index 变更的矛盾

### 问题描述

Skills Index 在以下情况会变化：
- 新增 Skill
- 删除 Skill
- 更新 Skill description

但每次变化都会使缓存的 system prompt 失效，命中率取决于 Skill 库的变动频率。

### 现状

官方没有公布缓存命中率的任何数据。对于高频使用 `hermes skills update` 的用户，每次对话的缓存效率可能很低。

---

## 八、单 Agent 承担所有工作的上下文耗尽

### 问题描述

Hermes 的消息循环是单 Agent 的（`for iteration in range(max_iterations)`），没有 OpenClaw 那样的 sub-agent 机制。

### 后果

- 上下文填满后，Context Compressor 介入（效果未知）
- 切换任务时，历史上下文仍然在（除非开新会话）
- 没有状态文件机制（OpenClaw 的 STATE.yaml）

### 规避方式

- 长任务使用独立的 fresh 会话
- 显式管理会话切换，不要在一个会话里混合多个不相关任务

---

## 九、不适合 Hermes 的场景清单

| 场景 | 原因 | 替代方案 |
|------|------|---------|
| 需要中间过程审计 | 整轮提交，中间状态不可恢复 | OpenClaw（JSONL append-only） |
| 长时间多步骤任务（需断点续传） | Cron 无状态持久化 | OpenClaw Flows |
| 需要随时查看任务进度 | 黑箱执行，无任务列表 | OpenClaw `flows list` |
| 复杂多 Agent 协作 | 单 Agent 循环，无 sub-agent 机制 | OpenClaw sessions_spawn/send |
| 需要精确中间上下文（合规场景） | Context Compressor 效果未知 | 记录到外部系统 |
| 高频 Skill 库变更（>100 skills 更新/月） | Skills Index 缓存效率低 | 定期批量更新而非实时 |

---

## 十、已经很好的场景（不要因为本文档过度规避）

以下场景 Hermes 表现良好，不要因为上面的失败模式而回避：

- 重复性代码工作流（效率提升 ~40%）
- 定时摘要 + 发送（"睡觉时它也在跑"）
- 跨会话的个人知识积累（FTS5 + SQLite）
- 移动端交互（Telegram/WhatsApp Gateway）
- 多模型成本优化（Auxiliary Models）

---

## 知识断层清单

1. **Context Compressor 实际效果**：压缩后信息保留率、摘要质量评价、幻觉率对比
2. **Prompt Caching 命中率**：Skill 库变动频率与缓存效率的实际数据
3. **`clarify_callback` / `sudo_callback` / `progress_callback` 完整行为**：函数签名、触发时机、返回值
4. **max_iterations=90 的边界行为**：接近上限时的具体动作（强制终止？警告？）
5. **GEPA 评估函数的权重机制**：谁决定了什么模式值得被 Skill 化

---

## 相关概念

- [[hermes-agent-message-loop]] — 消息循环机制（含四个回调详解）
- [[hermes-agent-best-practices]] — 最佳实践（正面参考）
- [[hermes-agent-learning-loop]] — GEPA 引擎与 Skill 生成
- [[heartbeat-vs-cron-philosophy]] — Hermes Cron 的设计取舍
- [[openclaw-best-practices]] — OpenClaw 最佳实践（对比参考）
