---
title: Hermes Agent 闭环学习系统（GEPA 引擎）
created: 2026-05-09
updated: 2026-05-11
type: concept
tags: [agent, hermes, self-improving, learning, gepa, skill]
sources: [raw/articles/hermes-agent-overview-2026.md, concepts/hermes-agent-source-code-architecture.md, concepts/hermes-agent-skills-system.md, concepts/hermes-agent-best-practices.md]
confidence: high
---

# Hermes Agent 闭环学习系统（GEPA 引擎）

## 一、什么是闭环学习

Hermes Agent 的核心创新在于**闭环学习系统（Learning Loop / Self-Evolution Engine）**，让 AI 能像人一样"越用越聪明"，而非每次对话都从零开始。

**本质**：每次处理复杂任务成功后，系统自动将解题模式提炼为可复用的 Skill，存入 `~/.hermes/skills/`。这是 Hermes 区别于其他 Agent 框架的核心能力。

---

## 二、GEPA 的两层含义（重要澄清）

现有 wiki 存在两种 GEPA 描述，它们不是矛盾的，而是反映了**两个不同抽象层级**的运作：

### 2.1 宏观层：Goal → Evaluation → Plan → Action

这是用户感知到的大阶段循环：

| 阶段 | 功能 |
|------|------|
| **Goal** | 记录用户最初目标，建立学习方向 |
| **Evaluation** | 任务执行完毕后自动评估结果质量：是否完全达成、中间步骤是否有冗余/错误、工具调用是否准确 |
| **Plan** | 回溯整个执行计划，分析哪些决策有效、哪些无效 |
| **Action** | 将高质量、成功的交互固化为可复用技能（Skill） |

### 2.2 微观层：Gather → Execute → Process → Assess

这是工具调用级别的 ReAct Loop 内循环：

| 阶段 | 功能 |
|------|------|
| **Gather** | 收集当前上下文（记忆、已有技能、用户输入） |
| **Execute** | 调用工具，执行下一步操作 |
| **Process** | 处理工具返回结果 |
| **Assess** | 评估当前状态是否达成目标，决定是否继续循环 |

**两层关系**：宏观层（GEPA）封装了多次微观层循环（ReAct）。当一个复杂任务完成后，进入宏观的 Evaluation → Plan → Action 阶段，生成 Skill。

---

## 三、技能自动生成机制

### 3.1 触发条件

来自最佳实践文档的量化指标：

- **工具调用次数 ≥ 5 次**的复杂任务才会触发评估
- 任务必须被判定为**成功完成**
- 发现有效的、可复用的工作流

> 注："5 次"是经验值还是可配置参数，文档未明确说明。

### 3.2 生成流程

在 `run_conversation()` 的最后一步执行：

```
用户任务完成
    ↓
Step 7: _evaluate_and_reflect(messages)  ← GEPA 的 E 阶段
    ↓ 判断是否满足生成条件
    ↓
满足条件 → _generate_skill() → Skill 文件（Markdown）
    ↓
Curator 追踪使用情况（use_count, view_count, patch_count）
```

### 3.3 Skill 文件格式

Skill 以 Markdown 格式存储，包含两部分：

**Frontmatter（YAML 头）**：
```yaml
---
name: <技能名>
description: "<技能用途描述>"
trigger: "<触发条件>"
created_by: "agent"   # 自动生成的标记
---
```

**正文内容**：操作意图 + 步骤描述 + 最佳实践要点。

> **设计选择**：Skill 存储的是"解题模式"而非"代码片段"。这意味着 Skill 被加载时，LLM 需要重新解读并实现，而非直接执行已有代码。这本质上是把"编译"推迟到了运行时——与 Hermes "Engine-First"的定位完全吻合。

### 3.4 后续维护

Curator（后台维护系统）负责：
- 追踪使用情况（use_count, view_count, patch_count）
- 标记闲置技能为 stale
- 归档长期未使用的技能
- 定期备份

---

## 四、与 OpenClaw Heartbeat 的本质对比

| | Hermes GEPA | OpenClaw Heartbeat |
|---|---|---|
| **触发条件** | 复杂任务完成后（被动） | 定时（every: 30m，主动） |
| **目的** | 提取并固化成功经验 | 执行例行检查、状态巡检 |
| **主动性** | 被动响应用户任务 | 主动定时唤醒 |
| **结果存储** | Skill（可复用） | heartbeat-state.json（状态记录） |
| **本质** | "事后聪明" | "定时警觉" |

**两者解决的是不同维度的问题**：GEPA 让 Agent 记住"怎么做"，Heartbeat 让 Agent 定期检查"有没有事要做"。

---

## 五、知识断层（诚实面对）

以下问题现有 wiki 和文档均未给出明确答案：

1. **评估函数内部逻辑**：`_evaluate_and_reflect()` 内部具体怎么判断"成功"？评估标准是什么？
2. **"5 次调用"是否为硬编码**：这个阈值是否可通过配置修改？
3. **Skill 版本管理**：同一触发条件的 Skill 多次生成时，是覆盖旧版还是创建新版本？
4. **Skill 冲突处理**：多个 Skill 同时触发时的优先级如何决定？
5. **生成 Token 开销**：Skill 生成需要额外一次 LLM 调用，成本影响未知
6. **Curator 算法细节**：stale 判定阈值、archive 策略的具体实现

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-source-code-architecture]] — 源码架构（含 AIAgent 类详细分析）
- [[hermes-agent-skills-system]] — 技能系统机制详解
- [[hermes-agent-memory-architecture]] — 四层记忆架构
- [[hermes-agent-best-practices]] — 最佳实践（含 Skill 生成时机说明）
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比分析
