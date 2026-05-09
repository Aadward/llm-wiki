---
title: OpenClaw v2026.3.28 版本发布
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [openclaw, release, security, xai-grok, minimax, image-generation, acp, requireApproval]
sources: [raw/articles/openclaw-v2026-3-28-release.md]
confidence: high
---

# OpenClaw v2026.3.28 版本发布

## 概述

OpenClaw v2026.3.28 发布于 2026 年 3 月 28 日，是 OpenClaw 从"极客玩具"走向"生产工具"的关键转折点。**核心主题：安全阀机制确立 + 搜索能力原生化 + 多模态整合**。

## 核心更新

### 🛡️ requireApproval —— 自主 Agent 的"安全阀"（最重要）

**机制**：`before_tool_call` 钩子中新增异步 `requireApproval` 能力。

当 Agent 即将执行高危操作（删除文件、发送消息、调用外部 API）时：
- 系统**暂停执行**，等待用户确认
- 多渠道审批：
  - Telegram 按钮
  - Discord 互动界面
  - /approve 命令（任意频道）
  - Exec 审批浮层（控制台 UI）

**设计意义**：
- Cisco 安全团队曾记录第三方 Skill 未授权数据外泄案例
- 引入意味着 OpenClaw 承认：**全自动不等于安全，受控自治才是正道**
- 这是 OpenClaw 补齐 Hermes 审批机制的重要节点，两者在审批能力上现已相当

**背景洞察**：功能出现时机恰好在中国政府限制国企使用 OpenClaw 之后，可能有监管压力背景。

### 🔍 xAI / Grok 深度整合

- Grok 搜索能力深度集成
- 搜索从"外挂功能"升级为"基础设施"
- 未来 Agent 竞争力 = "能获取多实时的信息来做任务"

### 🖼️ MiniMax 图像生成整合

- 新增 `image-01` 模型支持
- 同时支持：text-to-image、image-to-text、多模态对话
- **精简模型目录**：仅保留 M2.7，移除旧版本
- 版本策略：**不堆砌，而是整合**

### 🔗 ACP 多平台"当前对话绑定"

- `/acp spawn codex --bind here` 可将 Discord、BlueBubbles、iMessage 当前聊天直接变成 Codex 工作区
- 无需创建子线程
- **"Agent 即界面"理念**：对话本身就是工作台

## 版本演进时间线

| 时间 | 版本 | 关键变化 |
|------|------|----------|
| 2025.11 | Clawdbot | 首次发布 |
| 2026.1.27 | Moltbot | Anthropic 商标投诉，被迫更名 |
| 2026.1.30 | OpenClaw | 正式更名 |
| 2026.2.23 | v2026.2.23 | HTTP 安全头、SSRF 策略、配置脱敏 |
| 2026.3.11 | v2026.3.11 | WebSocket 源验证、插件隔离、session 沙盒（不可变版本）|
| **2026.3.28** | **v2026.3.28** | **requireApproval 安全阀、Grok 搜索、image-01** |
| 2026.4.5 | v2026.4.5 | 多媒体进核心、Prompt Cache 优化（见 [[openclaw-v2026-4-5-release]]）|

## OpenClaw ↔ Hermes 审批机制对比

| 维度 | OpenClaw requireApproval | Hermes approval_callback |
|------|-------------------------|-------------------------|
| **触发机制** | 异步 before_tool_call 钩子 | 同步回调 |
| **审批方式** | 多渠道（TG/DC/控制台/命令） | 超时自动拒绝（默认 60s） |
| **哲学** | fail-closed（默认暂停） | default-allow + callback |
| **集成深度** | 核心内置 | 回调机制 |

详见：[[security-model-comparison]]
