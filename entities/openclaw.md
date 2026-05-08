---
title: OpenClaw
created: 2026-05-08
updated: 2026-05-08
type: entity
tags: [agent]
sources: [raw/articles/openclaw-cookbook-2026-02.md]
confidence: high
---

# OpenClaw

AI Agent 平台（原名 ClawdBot、MoltBot），支持多 Agent 协作、子 Agent 调度、长期记忆系统和消息平台集成。

## Overview

OpenClaw 是 **persistent, tool-augmented AI assistant** — 一个持续运行、通过自然语言交互的 AI Agent。与单次 prompt 工具不同，OpenClaw Agent：

- 在会话间保持**长期记忆**（Markdown 文件持久化）
- 可以**生成子 Agent**（`sessions_spawn`）并行处理任务
- 通过**消息平台**（Telegram、Discord、WhatsApp）作为 UI
- 运行**定时任务**（cron）无需用户触发
- 拥有文件系统、SSH、API 等**工具访问权限**

## Key Primitives

| Primitive | 功能 |
|-----------|------|
| **Memory** | Markdown 文件持久化存储，跨会话保留 |
| **Sessions** | 独立 Agent 实例；`sessions_spawn` 创建子 Agent，`sessions_send` 发送消息 |
| **Skills** | 可复用能力模块，从 ClawHub 安装（视频剪辑、arXiv 阅读、Twitter 自动化等） |
| **MCP Servers** | Model Context Protocol 服务器，扩展工具访问能力 |
| **Cron/Heartbeat** | 定时自动任务，无需用户触发 |
| **AGENTS.md / SOUL.md** | 定义 Agent 身份、访问范围、行为规则 |
| **HEARTBEAT.md** | cron 调度表定义 |

## Key Patterns

- [[multi-agent-orchestration]] — CEO Pattern、Specialized Team、Chained Pipeline
- [[memory-knowledge-systems]] — 内置记忆、语义搜索、RAG 知识库
- [[scheduled-automation]] — 定时任务、Morning Briefing、Digest 模式
- [[security-credential-management]] — 防硬编码、TruffleHog、n8n 隔离

## Directory Structure

```
openclaw/
├── AGENTS.md          # Agent 身份、访问规则、路由逻辑
├── SOUL.md            # (可选) 个性化定义
├── HEARTBEAT.md       # cron 调度表
├── memory/            # 持久化 Markdown 记忆文件
│   ├── goals.md       # 目标和 OKR
│   ├── decisions.md   # 关键决策（append-only）
│   └── projects/      # 按项目组织
├── skills/            # 已安装技能（clawhub install）
└── .claud settings    # MCP servers、模型配置
```

## Source

- [awesome-openclaw-usecases](https://github.com/hesamsheikh/awesome-openclaw-usecases) — 42 个生产用例集合
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)