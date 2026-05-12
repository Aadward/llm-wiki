---
title: "Hermes Multi-Agent: 12 并行实例开发 Hermes 自身"
summary: "Teknium 每天并行运行 12 个 Hermes 实例开发 Hermes Agent，涵盖后端监控、RL 环境构建、数据集操控"
tags: [hermes, use-cases, multi-agent, dev-workflow, production]
sources: [raw/articles/hermes-agent-use-cases-2026.md]
confidence: high
---

# Hermes Multi-Agent: 12 并行实例开发 Hermes 自身

## 案例来源

> **@Teknium**（Hermes Agent 核心开发者）
> "I literally run 12 hermes agent instances every day in parallel to build Hermes Agent, and it's now a top 100 GitHub repositories of all time."

## 三支团队分工

| 团队 | 任务 | 使用模型 |
|------|------|----------|
| **Backend Team** | 监控和调查栈问题 | Hermes 主实例 |
| **Post-Training Team** | 创建新 RL 环境和基准、操控数据集 | Hermes + 专用 Agent |
| **Main Dev Loop** | 协调并行工作流、代码审查 | 12 × Hermes 并行 |

## Multi-Agent 协作架构

```
主 Agent（GPT-5.4）
    ↓ 分解任务
├── Coder Agent（MiniMax M2.7）→ 实现功能
├── QA Agent（Local Qwen 35B A3B）→ 测试验证
├── RL Agent（Hermes）→ 创建强化学习环境
└── Data Agent（Hermes）→ 数据集调查和操控
    ↓
主 Agent → 修复 → Ship
```

**关键设计**：
- 每个 Agent 用 Worktree 模式隔离 git 状态（`hermes -w`）
- 独立进程，不共享上下文，避免记忆污染
- 主 Agent 负责任务分发和最终合并

## 工程价值

1. **证明了规模可行性**：12 并行实例稳定运行，top 100 GitHub 仓库
2. **自我吞噬**：用 Hermes 开发 Hermes，最大化真实场景测试
3. **分工专业化**：不同 Agent 用最适合的模型

## 相关概念

- [[hermes-agent-best-practices]] — 最佳实践完整指南
- [[multi-agent-collaboration]] — OpenClaw ↔ Hermes 多 Agent 协作对比
- [[hermes-agent-profiles-multi-instance]] — Profiles 多实例管理
