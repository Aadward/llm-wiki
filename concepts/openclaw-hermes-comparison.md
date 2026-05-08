---
title: OpenClaw ↔ Hermes Agent 深度对比分析
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [openclaw, hermes, comparison, architecture, skills, memory]
sources: [web/qiniushanghai.com, web/jdon.com/91503-openclaw-hermes-agent-skills-comparison.html, web/blog.csdn.net/MuXinShu1/article/details/160115112]
confidence: high
---

# OpenClaw ↔ Hermes Agent 深度对比分析

## 概述

OpenClaw（龙虾）和 Hermes Agent（爱马仕）是 2026 年最火热的两个开源 AI Agent 框架。两者定位不同，各有优势：OpenClaw 是"网关优先"的助手控制平面，Hermes 是"学习优先"的智能体运行时。

---

## 一、核心数据对比

| 对比维度 | OpenClaw | Hermes Agent |
|----------|-----------|--------------|
| **GitHub Stars** | 356,650+ | 73,800+ |
| **最新版本** | v2026.4.12 | v0.8.0 |
| **主语言** | TypeScript | Python |
| **开源协议** | MIT | MIT |
| **定位** | 本地优先个人 AI 助手 | 自进化 AI Agent |
| **渠道数量** | 25+ | 14+ |
| **技能数量** | 44,000+ | 648+ |
| **开发者** | Peter Steinberger（独立开发者） | Nous Research |

---

## 二、核心哲学对比

### 2.1 OpenClaw：网关模式（Gateway-First）

**核心理念**：配置即行为

> OpenClaw 本身没有"思考能力"，它是一个 AI 智能体执行网关——为各类大模型装上"手脚"，让 AI 从"只说不做"变成"动手干活"。

- **本地优先**：数据全程存储在用户本地/私有云，敏感信息不出内网
- **强执行能力**：文件读写、终端脚本、浏览器拟人化操作、API 调用
- **庞大生态**：44,000+ 社区 Skill，覆盖办公、开发、生活等高频场景
- **多入口**：WebUI、CLI、HTTP API，可对接飞书、钉钉等 IM 机器人

### 2.2 Hermes Agent：引擎模式（Engine-First）

**核心理念**：The agent that grows with you

- **闭环学习**：每次完成复杂任务后自动创建和优化 Skill
- **四层记忆**：工作记忆 → 情景记忆 → 技能记忆 → FTS5 检索
- **自进化**：越用越聪明，Skill 持续迭代优化
- **并行子 Agent**：支持多 Agent 协作和任务拆分

---

## 三、架构对比

### 3.1 OpenClaw 架构

```
用户（WhatsApp/Telegram/Slack/飞书）
    ↓
Gateway（网关层）—— 神经中枢
    ↓
Core（核心运行时）—— Agent 思考引擎
    ↓
Model + Skills + Memory
```

**特点**：
- TypeScript + Node.js，基于 Electron 打包桌面端
- 中心化 Gateway 进程统一管理所有消息路由和会话状态
- 微内核设计，预执行流程审计友好

### 3.2 Hermes Agent 架构

```
用户（CLI/Telegram/Discord/飞书）
    ↓
AIAgent Core（核心 Agent）
    ↓
GEPA 循环（Gather → Execute → Process → Assess）
    ↓
Model + Tools + Memory（SQLite + FTS5）
```

**特点**：
- Python 主语言，双入口（CLI + 消息平台）共享同一核心
- 分布式设计，无单点瓶颈
- 云原生友好，原生支持 Kubernetes 部署

---

## 四、记忆系统对比

### 4.1 OpenClaw：纯 Markdown 文件

- **单一源真理**：一切记忆以纯 Markdown 文件为存储介质
- **会话状态**：JSONL append-only 日志 + 自动 compaction
- **检索**：SQLite-vec 驱动 hybrid 搜索（vector + BM25 + MMR + temporal decay）
- **风险**：上下文无限膨胀，推高推理成本

### 4.2 Hermes Agent：四层记忆架构

- **第一层**：工作记忆（Working Memory）— 当前会话
- **第二层**：情景记忆（Episodic Memory）— 跨会话事实和偏好
- **第三层**：技能记忆（Procedural Memory）— 可复用 Skill
- **第四层**：FTS5 全文检索
- **优势**：仅将高价值决策逻辑固化为 Skill，避免上下文膨胀

---

## 五、技能系统对比

### 5.1 OpenClaw：用户主导型

**特点**：
- 技能必须由用户主导添加
- 严格治理，精确控制
- 44,000+ 社区 Skill 可选
- 技能冲突：多个技能同时触发时可能冲突

**代表技能生态**：ClawHub（44,000+ 社区贡献）

### 5.2 Hermes Agent：自生成型

**特点**：
- Agent 自动生成和优化 Skill
- 系统提示嵌入隐形推动机制
- 每调用 N 次工具，代理考虑保存当前模式为 Skill
- **风险**：技能爆炸和冗余困境

**代表技能生态**：agentskills.io（648+ Skill）

---

## 六、选择建议

| 场景 | 推荐 |
|------|------|
| 需要 25+ 渠道接入（微信、飞书等） | OpenClaw |
| 需要自我进化的长链路任务 | Hermes |
| 需要严格权限控制和安全审计 | OpenClaw |
| 需要快速构建标准化、流程化 AI 应用 | OpenClaw |
| 需要极简开箱即用体验 | Hermes |
| 需要并行子 Agent 协作 | Hermes |
| 需要大规模企业级部署 | Hermes |
| 个人日常助手，多平台覆盖 | OpenClaw |

### 一句话总结

> **OpenClaw 像安卓系统——高度开放、插件丰富、但需要用户折腾维护；Hermes 像苹果系统——开箱即用、自动进化、但封闭生态。**

---

## 相关概念

- [[openclaw]] — OpenClaw 整体介绍
- [[hermes-agent]] — Hermes Agent 整体介绍
- [[openclaw-source-code-architecture]] — OpenClaw 源码架构
- [[openclaw-clawhub]] — ClawHub 技能生态
