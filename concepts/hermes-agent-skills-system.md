---
title: Hermes Agent 技能系统
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, skill, workflow, automation]
sources: [raw/articles/hermes-agent-overview-2026.md]
confidence: high
---

# Hermes Agent 技能系统（Skills System）

## 概述

Hermes Agent 的技能系统是其**自我进化能力**的核心载体。每次处理复杂任务成功后，系统会自动将解题模式提炼为可复用的**技能（Skill）**，存入 `~/.hermes/skills/` 目录。

## 技能的本质

技能是以 **Markdown 格式**存储的文档，包含：
- **触发条件**：何时使用这个技能
- **操作步骤**：具体执行流程
- **最佳实践**：成功的关键要点
- **版本历史**：随使用不断优化

## 技能生命周期

```
发现任务 → 分析模式 → 创建技能 → 存储技能
                              ↓
使用技能 ← 加载技能 ← 检索技能 ←
     ↓
根据反馈 → 优化技能（迭代改进）
```

## 技能分类

### 内置技能（Built-in Skills）

Hermes Agent 开箱即带 **40+ 内置技能**，涵盖：

| 类别 | 示例技能 |
|------|----------|
| 软件开发 | 代码审查、测试、重构 |
| 知识管理 | wiki 管理、笔记整理 |
| 自动化 | 定时任务、数据采集 |
| 研究 | 论文搜索、摘要生成 |

### 用户自定义技能（User Skills）

用户可创建自定义技能，存放于 `~/.hermes/skills/`：

```markdown
---
name: my-workflow
description: "描述技能用途"
trigger: "触发条件"
---

# 技能内容
1. 步骤一
2. 步骤二
```

### Agent 自动生成技能（Auto-generated）

Hermes 的 GEPA 引擎会自动从成功经验中生成技能：
- 处理复杂任务后自动提炼
- 保存到 `created_by: "agent"` 的技能
- 由 Curator 自动维护生命周期

## Curator 自动维护

Curator 是 Hermes Agent 的后台维护系统，负责：
- 追踪技能使用情况（use_count, view_count, patch_count）
- 标记闲置技能为 stale
- 归档过技能
- 定期备份

## 技能加载方式

```bash
# 加载特定技能
/skill <name>

# 预加载技能（启动时）
hermes -s skill1,skill2

# 查看所有技能
hermes skills list
```

## 与 OpenClaw Extension 的区别

| 维度 | OpenClaw Extension | Hermes Skill |
|------|---------------------|--------------|
| 格式 | Python/JS 代码 | Markdown 文档 |
| 版本控制 | ❌ | ✅ Git 友好 |
| 自我优化 | 部分 | ✅ GEPA 引擎驱动 |
| 加载方式 | 代码级集成 | 文档级加载 |

---

## 本文定位说明

本文档讲述**技能系统的底层机制**（技能是什么、如何生成、Curator 生命周期）。

**与 [[hermes-agent-skill-library|官方技能库]] 的区别**：
- 本文：技能如何工作（生成→存储→加载→优化机制）
- skill-library：648 个官方/社区技能的使用方法和 CLI 命令

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-learning-loop]] — 闭环学习系统（技能生成的触发引擎）
- [[hermes-agent-skill-library]] — 官方技能库生态和使用命令 [[★ 互补阅读]]
- [[scheduled-automation]] — 定时自动化（技能应用场景）
