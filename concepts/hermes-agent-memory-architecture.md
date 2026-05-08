---
title: Hermes Agent 四层记忆架构
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, memory, architecture, persistence]
sources: [raw/articles/hermes-agent-overview-2026.md]
confidence: high
---

# Hermes Agent 四层记忆架构

## 概述

Hermes Agent 采用**四层记忆架构**，模拟人类认知的不同层次，实现跨会话的持久智能。

## 四层记忆详解

### L1: Working Memory（工作记忆）

- **生命周期**：当前会话
- **容量**：受限于模型的 context window
- **作用**：维持单次对话的连贯性，存储即时上下文
- **类比**：人类的"工作台"——处理当前任务的即时工作区

### L2: Episodic Memory（情景记忆）

- **生命周期**：永久保存
- **存储方式**：SQLite + FTS5 全文检索
- **作用**：跨会话记住事实、偏好、经历
- **类比**：人类的"日记本"——记录跨时间的事件和知识

**实际例子**：
```
第 1 天："我的项目用的是 Next.js 14 + TypeScript + TailwindCSS"
第 30 天："帮我加个新页面"
→ Hermes 自动检索到项目技术栈记忆，直接用相应技术栈创建页面
```

### L3: MEMORY.md（环境记忆）

- **生命周期**：永久保存
- **内容**：环境事实、经验教训、项目上下文
- **形式**：Markdown 文件，可版本控制
- **类比**：人类的"笔记本"——记录重要的环境信息

### L4: USER.md（用户画像）

- **生命周期**：永久保存
- **内容**：用户职业、目标、偏好、习惯
- **形式**：Markdown 文件
- **类比**：人类的"自我认知"——了解自己是谁、需要什么

## 记忆检索流程

```
用户输入 → 检查 Working Memory（当前上下文）
         → 检查 Episodic Memory（跨会话检索）
         → 检查 MEMORY.md（环境事实）
         → 检查 USER.md（用户画像）
         → 综合输出响应
```

## 与 OpenClaw 记忆系统对比

两者都支持跨会话记忆，但实现重点不同：

| 维度 | OpenClaw | Hermes Agent |
|------|----------|--------------|
| 存储方式 | SQLite | SQLite + Markdown 文件 |
| 检索方式 | 语义搜索 | FTS5 + 可调用的 session_search |
| 用户画像 | Memory Blocks | USER.md |
| 技能沉淀 | ✅ | ✅ 更强的 Skill 系统 |

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-learning-loop]] — 闭环学习系统
- [[memory-knowledge-systems]] — 通用记忆与知识系统
