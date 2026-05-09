---
title: Hermes Agent 闭环学习系统（GEPA 引擎）深度解析
created: 2026-05-09
updated: 2026-05-13
type: concept
tags: [agent, hermes, self-improving, learning, gepa, skill, curator]
sources: [agent/prompt_builder.py, agent/curator.py, tools/skill_manager_tool.py, cli.py]
confidence: high
---

# Hermes Agent 闭环学习系统（GEPA 引擎）深度解析

## 一、核心澄清：GEPA 不是什么

**之前 wiki 的错误：**

- ❌ 认为存在 `_evaluate_and_reflect()` 函数，在任务完成后自动评估并创建 Skill
- ❌ 认为"工具调用 ≥ 5 次"是自动触发 Skill 生成的阈值
- ❌ 认为 GEPA 是一个在代码中实现的闭环评估引擎

**实际情况：**

- Hermes **没有**自动执行评估和 Skill 创建的后置钩子
- "5+ tool calls" 不是程序级别的触发条件，而是写在系统提示词里的经验法则
- Skill 的创建完全是由 **LLM 自己根据系统提示词中的指引来决定是否调用 `skill_manage`**

---

## 二、真正的 Skill 创建机制

### 2.1 触发逻辑：SKILLS_GUIDANCE

Skill 创建的触发来自 `agent/prompt_builder.py` 中的 `SKILLS_GUIDANCE`：

```
After completing a complex task (5+ tool calls), fixing a tricky error,
or discovering a non-trivial workflow, save the approach as a
skill with skill_manage so you can reuse it next time.

When using a skill and finding it outdated, incomplete, or wrong,
patch it immediately with skill_manage(action='patch') — don't wait to be asked.
Skills that aren't maintained become liabilities.
```

这段文字在每次对话开始时注入到系统提示词中。**Skill 的创建是由 LLM 自行判断的**，当它认为某个任务的解决方案值得保留时，它会调用 `skill_manage(action='create')` 工具。

### 2.2 创建流程

```
用户任务完成
    ↓
模型判断：这个解决方案值得保存为 Skill
    ↓
调用 skill_manage(action='create', name='...', content='...')
    ↓
创建 ~/.hermes/skills/<skill-name>/SKILL.md
    ↓
Curator 在后续自动追踪使用情况（use_count 等）
```

**关键结论：Skill 创建是 LLM 的主动行为，不是系统的被动响应。**

---

## 三、Curator 的真实角色

### 3.1 Curator 不创建 Skill，只维护 Skill

Curator 是一个**后台维护进程**，由 `cli.py` 在启动时或 `gateway/run.py` 在 cron tick 时触发。

**Curator 的职责：**

| 职责 | 说明 |
|------|------|
| 状态管理 | 将闲置技能标记为 `stale`（30天不用），将长期归档为 `archived`（90天不用） |
| 合并整合 | 将窄范围的同簇 Skill 合并为宽范围的"伞形 Skill" |
| 恢复 | Skill 被标记为 stale 后如果再次被使用，自动恢复为 `active` |
| 绝不删除 | 只归档（移到 `.archive/`），不删除任何 Skill |

**Curator 触发的条件（全部满足才运行）：**

- 距离上次运行 ≥ `interval_hours`（默认 168 小时 = 7 天）
- 距离上次用户交互 ≥ `min_idle_hours`（默认 2 小时）
- `curator.enabled = true`（默认开启）

### 3.2 Curator 的合并哲学

从 `CURATOR_REVIEW_PROMPT` 中可以看到 Curator 的核心设计思想：

> "A collection of hundreds of narrow skills where each one captures one session's specific bug is a FAILURE of the library — not a feature."

Curator 追求的是**类级别的伞形技能**：

- ❌ 100 个 narrow skills：`pr-fix-001`, `pr-fix-002`, `pr-review-001`...
- ✅ 1 个伞形 Skill：`pr-workflow`，含多个 labeled subsection

### 3.3 Curator 的执行方式

Curator 实际是**启动一个全新的 AIAgent 实例**（fork），运行 `CURATOR_REVIEW_PROMPT`，让这个后台 Agent 调用 `skill_manage` 来执行实际操作。这个后台 Agent 使用辅助模型（auxiliary client），不参与主对话循环。

---

## 四、GEPA 的两层含义（重新诠释）

### 4.1 宏观层：Goal → Evaluation → Plan → Action

这是用户感知到的大阶段，其中：

- **Goal**：用户原始目标
- **Evaluation**：这里**没有**代码级的评估函数。实际上是 LLM 在生成响应前的自我判断："这个问题我解决得好吗？"
- **Plan**：LLM 决定下一步操作
- **Action**：执行工具调用

**关键区别：Evaluation 和 Action 阶段不存在自动触发的 Skill 创建代码。**

### 4.2 微观层：Gather → Execute → Process → Assess

这是工具调用级别的 ReAct Loop 内循环：

| 阶段 | 功能 |
|------|------|
| Gather | 收集当前上下文（记忆、已有技能、用户输入） |
| Execute | 调用工具，执行下一步操作 |
| Process | 处理工具返回结果 |
| Assess | 评估当前状态是否达成目标，决定是否继续循环 |

### 4.3 两层关系重新理解

```
用户任务
  ↓
宏观 GEPA（Goal → Evaluation → Plan → Action）
  ↓
  其中 Action 阶段触发多次微观 ReAct Loop（Gather → Execute → Process → Assess）
  ↓
任务完成，模型根据 SKILLS_GUIDANCE 自主决定是否创建 Skill
```

**这不是一个自动化的机器学习闭环，而是一个人机协作的指导框架：Hermes 告诉 LLM"在什么情况下应该把经验保存为 Skill"，LLM 自己判断并执行。**

---

## 五、与 OpenClaw Heartbeat 的对比（修正）

| | Hermes GEPA（实际） | OpenClaw Heartbeat |
|---|---|---|
| **触发机制** | LLM 自主决定（根据 SKILLS_GUIDANCE） | 定时唤醒（every: 30m） |
| **执行者** | 主模型在对话中调用 `skill_manage` | 独立进程读取 heartbeat-state.json |
| **结果** | 创建/更新 Skill 文件 | 更新状态文件，执行例行检查 |
| **主动性** | 被动（依赖模型判断） | 主动定时 |
| **本质** | "我觉得值得记" | "到点了该检查" |

**两者真正的区别**：Hermes 的学习是**机会型的**（遇到值得记住的就记），OpenClaw 的 Heartbeat 是**时间型的**（定期强制检查）。

---

## 六、这个设计选择意味着什么

### 6.1 优点

- **低实现复杂度**：不需要写评估函数，不需要定义"成功"的量化标准
- **灵活性**：LLM 可以根据上下文判断什么是值得保存的，超出简单规则的范畴
- **模型自主性**：Skill 的创建是模型主动行为，更符合"学会"的本质

### 6.2 风险

- **不稳定性**：依赖模型的判断，不同模型、不同上下文可能产生不同的 Skill 创建决策
- **质量不一致**：没有客观标准，Skill 的质量和完整性完全取决于模型
- **机会型遗漏**：如果某次对话模型没有识别出值得保存的经验，这个经验就丢失了

### 6.3 Hermes 的"进化"本质

Hermes 的"自我进化"不是真正意义上的机器学习闭环（没有梯度、没有参数更新），而是一个**基于人机协作的经验固化机制**：

- 人（Hermes 团队）给模型提供指导原则（SKILLS_GUIDANCE）
- 模型根据指导原则自主执行（调用 skill_manage）
- Curator 负责清理和整合，防止知识库腐烂

这更像是一种**工程化的最佳实践积累**，而不是 AI 自发形成的知识进化。

---

## 七、知识断层（诚实面对）

以下问题仍然没有确切答案：

1. **模型如何判断"复杂任务"**：除了"5+ tool calls"这个经验法则，有没有其他隐含的判断标准？
2. **Skill 冲突处理**：多个 Skill 描述相似或重叠时，模型如何决定是更新现有 Skill 还是创建新的？
3. **Skill 版本管理**：同一触发条件的 Skill 多次生成时，是覆盖还是创建新版本？
4. **生成 Token 开销**：Skill 生成需要额外一次 LLM 调用，这个成本在实践中是否可接受？
5. **Curator 与主模型的协作**：Curator 的合并决策是否会和主模型创建的 Skill 产生冲突？

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-message-loop]] — 消息循环机制
- [[hermes-agent-memory-architecture]] — 四层记忆架构
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比分析
- [[openclaw-heartbeat]] — OpenClaw Heartbeat 机制（待补充）
