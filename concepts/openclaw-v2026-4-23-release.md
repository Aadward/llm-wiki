---
title: OpenClaw v2026.4.23 版本发布
created: 2026-05-10
updated: 2026-05-10
type: concept
tags: [openclaw, version-release, gpt-5, subagent, image-generation]
sources: [raw/articles/openclaw-v2026-4-23-release.md]
confidence: high
---

# OpenClaw v2026.4.23 版本发布

## 概述

OpenClaw v2026.4.23 发布于 2026年4月25日，是 v2026.4.x 功能主线的重要版本。

## 核心更新

### GPT-5.5 落地
本次发布把"大脑"换成了 GPT-5.5，相当于给机器人换了一块更聪明的芯片。推理能力、多模态理解进一步提升。

### 双通道图像生成
新增两条图像生成通道：
- 一条使用 Codex API
- 支持更高质量的图像生成

### 子智能体分支上下文机制
引入**子智能体分支上下文机制**，这是本版本最重要的架构性更新：
- 子智能体可以"继承记忆"
- 任务分支现在可以保留上下文
- 复杂任务的分支探索变得更可靠

### 本地嵌入模型优化
- 优化本地嵌入模型
- 工具超时控制改善

## 版本系列脉络

| 版本 | 日期 | 主题 |
|------|------|------|
| v2026.4.14 | 2026-04-14 | 安全加固 + 可靠性 |
| v2026.4.20 | 2026-04-20 | Kimi K2.6 + 分层定价 |
| v2026.4.22 | 2026-04-22 | 腾讯混元 + 多模态闭环 |
| **v2026.4.23** | **2026-04-25** | **GPT-5.5 + 子智能体上下文** |
| v2026.4.24 | 2026-04-25 | DeepSeek V4 + Google Meet |

## 相关页面
- [[openclaw-v2026-4-14-release]] — 安全加固版本
- [[openclaw-v2026-4-24-release]] — DeepSeek V4 + 插件性能飞跃
- [[openclaw-gateway-architecture]] — 网关架构
