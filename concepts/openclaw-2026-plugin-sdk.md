---
title: OpenClaw 2026 Plugin-SDK 重构
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [agent, skill, security]
sources: [raw/articles/openclaw-2026-plugin-sdk-overhaul.md]
confidence: high
---

# OpenClaw 2026 Plugin-SDK 重构

## 概述

2026.3.x 版本对插件系统进行了**彻底重构**，是自 OpenClaw 诞生以来最大的一次 breaking change。

## 变更内容

### 插件系统"换骨"

- **旧的扩展 API**（`openclaw/extension-api`）被**完全移除**
- 取而代之的是全新的模块化 **plugin-sdk**
- 旧插件（基于 `extension-api`）必须完全重写才能在 2026.3+ 运行
- 新插件系统采用**可插拔架构**，支持自定义上下文处理逻辑

### Context Engine（可插拔上下文引擎）

2026.3.7 引入的 Context Engine 允许开发者：
- 通过插件接口**自定义上下文处理**的逻辑与策略
- 无需修改 OpenClaw 核心代码
- 为特定用例优化上下文窗口使用

### 模型支持更新

| 版本 | 新增模型支持 |
|------|-------------|
| 2026.2.17 | Sonnet 4.6（1M 上下文） |
| 2026.2.23 | Claude Opus 4.6 |
| 2026.3.7 | GPT-5.4、Gemini 3.1 Flash/Lite |
| 2026.3.8 | 正式支持 GPT-5.4 |

### 安全策略变更（2026.2.23）

- 浏览器 SSRF 策略默认调整为 `"trusted-network"` 模式
- 私有网络用户需显式配置
- 迁移命令：`openclaw doctor --fix`

## 影响

- **插件开发者**：必须使用新 plugin-sdk 重写插件
- **普通用户**：如使用旧插件，需等待作者更新或自行迁移
- **安全提升**：新架构带来更好的隔离性和可控性

## 相关概念

- [[multi-agent-orchestration]] — 多 Agent 架构，新插件系统是其扩展基础
- [[security-credential-management]] — 安全加固是新版本的另一个核心主题
