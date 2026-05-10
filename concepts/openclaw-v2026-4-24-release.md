---
title: OpenClaw v2026.4.24 版本发布
created: 2026-05-10
updated: 2026-05-10
type: concept
tags: [openclaw, version-release, deepseek-v4, google-meet, voice-agent, plugin]
sources: [raw/articles/openclaw-v2026-4-24-release.md]
confidence: high
---

# OpenClaw v2026.4.24 版本发布

## 概述

OpenClaw v2026.4.24 发布于 2026年4月25日，是一次涵盖核心代理框架、多平台渠道集成、模型生态、浏览器自动化、语音通话、诊断可观测性等数十个模块的**超大版本更新**。

## 核心更新

### DeepSeek V4 成为默认模型
- **DeepSeek V4 Flash** 正式取代 Claude Sonnet 成为新用户上手默认模型
- **V4-Pro** 同步加入内置目录
- 成本直降 **17倍**（对比 Claude Sonnet）

### Google Meet 原生集成
- 引入 Google Meet 作为**原生捆绑参与者插件**
- AI 可以直接 join 视频会议并参与讨论

### 语音通话直达完整智能体
- 语音通话不再只是语音转文字
- 现在可以端到端连接完整 Agent 执行能力
- 实现真正的语音驱动的 Agent 交互

### 插件架构性能飞跃
插件系统全面重写，性能大幅提升：
- 场景1：**1秒 → 43毫秒**（提升 ~23倍）
- 场景2：**265毫秒 → 8毫秒**（提升 ~33倍）
- 涵盖 Google Live Talk 等插件

### 浏览器自动化改进
- 升级坐标点击与恢复能力
- 任务中断后可更可靠地恢复

### 问题修复
- Telegram、Slack、MCP、会话、TTS 多项修复

## 降本价值

DeepSeek V4 Flash 取代 Claude Sonnet 作为默认模型，对于：
- 新用户引导：成本大幅降低
- 日常任务：V4 Flash 性价比最优
- 高端任务：V4-Pro 提供更强能力

## 相关页面
- [[openclaw-v2026-4-23-release]] — GPT-5.5 + 子智能体上下文
- [[openclaw-hermes-comparison]] — OpenClaw vs Hermes 对比表（含最新版本）
- [[cost-optimization-comparison]] — 成本优化对比
