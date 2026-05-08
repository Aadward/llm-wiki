---
title: Hermes Agent
created: 2026-05-09
updated: 2026-05-09
type: entity
tags: [agent, framework, nous-research, open-source, self-improving]
sources: [raw/articles/hermes-agent-overview-2026.md]
---

# Hermes Agent

## Overview

**Hermes Agent** 是由 [Nous Research](https://nousresearch.com) 开发的高人气开源 AI Agent 框架（GitHub 59k+ Stars），核心定位是 *"The agent that grows with you"* —— 一个随你成长的持久化个人代理。

与一次性聊天工具不同，Hermes Agent 内置**自学习闭环**，能在解决复杂任务后自动将成功经验总结为可复用技能（Skill），并在后续对话中检索历史记忆，持续优化对用户的理解。

| 属性 | 值 |
|------|-----|
| **开发者** | Nous Research |
| **开源时间** | 2026 年 2 月 |
| **许可证** | MIT |
| **官方文档** | [hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com) |
| **GitHub** | [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) |
| **支持平台** | Linux, macOS, WSL2, Android (Termux) |

## 核心差异化定位

Hermes Agent 处于与 [Claude Code](./claude-code.md)、[OpenClaw](./openclaw.md)、Cursor 同类产品赛道，但核心差异在于**自我进化能力**：

| 维度 | Claude Code | OpenClaw | Hermes Agent |
|------|------------|----------|--------------|
| **核心定位** | 代码仓库工程 Agent | 全天候自动化生活/办公 | **随你成长的持久代理** |
| **自我进化** | ❌ | 部分 | ✅ 闭环学习 + GEPA 引擎 |
| **记忆持久性** | 会话级 | 跨会话 | 永久记忆 + 技能沉淀 |
| **消息网关** | ❌ | ✅ | ✅ 10+ 平台 |
| **模型无关** | 仅 Claude | 仅 Claude | 20+ 提供商 |

## 三大核心能力

### 1. 闭环学习系统（Learning Loop）

Hermes Agent 的核心创新是 **GEPA 引擎**（Goal-Evaluation-Plan-Action）：

```
Goal（目标）→ Evaluation（评估）→ Plan（计划）→ Action（行动）→ 循环优化
```

- **Goal**：记录用户最初提出的目标
- **Evaluation**：任务完成后自动评估结果质量
- **Plan**：回溯执行计划，分析有效/无效决策
- **Action**：将成功的交互固化为可复用技能

### 2. 四层记忆架构

| 层级 | 类型 | 生命周期 | 说明 |
|------|------|----------|------|
| L1 | Working Memory | 当前会话 | 即时上下文，维持对话连贯性 |
| L2 | Episodic Memory | 永久 | 跨会话事实、偏好、经历（SQLite + FTS5） |
| L3 | MEMORY.md | 永久 | 环境事实、经验教训 |
| L4 | USER.md | 永久 | 用户画像：职业、目标、偏好 |

### 3. 技能引擎（Skills System）

每次处理新任务后，Hermes 可自动将解题模式保存为 **Markdown 格式的技能文件**（`~/.hermes/skills/`），这些技能：
- 便于版本控制（Git 管理）
- 会随使用不断优化改进
- 可在任意会话中加载复用

## 支持的 LLM 提供商（20+）

OpenRouter, Anthropic, OpenAI, DeepSeek, Google Gemini, xAI/Grok, Hugging Face, Z.AI/GLM, **MiniMax**, Kimi/Moonshot, Alibaba/DashScope, Nous Portal, GitHub Copilot, 以及自定义端点。

## 消息网关平台（10+）

Telegram, Discord, Slack, WhatsApp, Signal, Matrix, Email, SMS, Mattermost, Home Assistant, DingTalk, Feishu（飞书）, WeCom, BlueBubbles (iMessage), WeChat, API Server, Webhooks。

## 安装方式

```bash
# 一行命令安装（Linux/macOS/WSL2）
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
source ~/.bashrc && hermes

# 初始化配置
hermes setup
hermes gateway setup
```

## 相关链接

- 官网：https://hermes-agent.nousresearch.com
- 文档：https://hermes-agent.nousresearch.com/docs/
- GitHub：https://github.com/NousResearch/hermes-agent
- Discord：https://discord.gg/nous-research
