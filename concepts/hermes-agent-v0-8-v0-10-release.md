---
title: Hermes Agent v0.8.0 / v0.10.0 版本解析
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [hermes-agent, release, configuration, live-switching]
sources: [raw/articles/hermes-agent-v0-8-v0-10-release.md]
confidence: high
---

# Hermes Agent v0.8.0 / v0.10.0 版本解析

## v0.8.0 核心功能

### Live Model Switching(实时切换)

这是 v0.8.0 最重要的功能特性。在 Telegram/Discord/Slack 等消息平台的对话中,无需重启即可实时切换底层 LLM。

**使用场景:**
- 动态测试不同模型能力
- 根据任务类型选择最适合的模型
- 在单一会话中切换模型而不中断上下文

### hermes model 向导

通过统一的 `hermes model` 向导配置任意 LLM 提供商,**无需手动编辑配置文件**。

### 支持的提供商

| 提供商 | 特点 |
|--------|------|
| Nous Portal | 原生 Hermes 系列 |
| OpenRouter | 200+ 模型统一接入 |
| OpenAI | 标准 OpenAI 兼容 |
| Kimi | 国内直连,无需代理 |
| MiniMax | 国内直连,无需代理 |

### Live Model Switching vs OpenClaw 模型切换

OpenClaw 也支持多模型,但需要重启会话。Hermes 的 Live Model Switching 允许在**同一会话中**无缝切换,保留了上下文。

## v0.10.0 补充信息

- Windows 安装教程支持(WSL2)
- 进一步稳定性和兼容性提升

## 版本演进

v0.8.0 是第一个支持 Live Model Switching 的版本,标志着 Hermes 从"配置-重启"模式向"动态路由"模式的演进。

## 关联概念

- [[hermes-agent-v0-12-release]] - v0.12 版本演进
- [[openclaw-hermes-decision-tree]] - 选型对比
- [[hermes-agent-message-loop]] - Hermes 消息循环
