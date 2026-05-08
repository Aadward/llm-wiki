---
title: Hermes Agent 闭环学习系统
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, self-improving, learning, gepa]
sources: [raw/articles/hermes-agent-overview-2026.md]
confidence: high
---

# Hermes Agent 闭环学习系统

## 什么是闭环学习

Hermes Agent 的核心创新在于**闭环学习系统（Learning Loop / Self-Evolution Engine）**，让 AI 能像人一样"越用越聪明"，而非每次对话都从零开始。

## GEPA 引擎

闭环学习的核心是 **GEPA 系统**（Goal-Evaluation-Plan-Action）：

```
Goal（目标）
    ↓
Evaluation（评估）
    ↓  
Plan（计划）
    ↓
Action（行动）
    ↓
    ↺ 回到 Goal 形成循环
```

### 各阶段详解

| 阶段 | 功能 |
|------|------|
| **Goal** | 记录用户最初提出的目标，建立学习方向 |
| **Evaluation** | 任务执行完毕后自动评估结果质量：是否完全达成、中间步骤是否有冗余/错误、工具调用是否准确 |
| **Plan** | 回溯整个执行计划，分析哪些决策有效、哪些无效 |
| **Action** | 将高质量、成功的交互固化为可复用技能（Skill） |

## 技能提炼机制

当 Hermes Agent 完成一个复杂任务后，它会自动：

1. **提取操作序列** — 分析完成任务的步骤
2. **抽象为通用模式** — 将具体操作转化为可复用的工作流
3. **保存为 Skill 文件** — 以 Markdown 格式存入 `~/.hermes/skills/`
4. **持续优化** — 后续使用时根据反馈不断改进技能

## 与传统 Agent 的区别

| 特性 | 传统 Agent | Hermes Agent |
|------|------------|--------------|
| 跨会话学习 | ❌ 每次从零开始 | ✅ 记忆 + 技能沉淀 |
| 经验复用 | ❌ 无法复用 | ✅ Skill 系统 |
| 自我优化 | ❌ 固定行为 | ✅ GEPA 引擎评估迭代 |
| 用户理解 | ❌ 无用户画像 | ✅ USER.md 持久画像 |

## 相关概念

- [[hermes-agent]] — 整体框架
- [[memory-knowledge-systems]] — 记忆与知识系统
- [[persistent-agent-patterns]] — 持久 Agent 核心模式
