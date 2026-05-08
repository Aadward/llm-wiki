---
title: Hermes Agent v0.12.0 版本发布
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [hermes-agent, release, slash-commands, multi-session, version-history]
sources: [raw/articles/hermes-agent-v0-12-release.md]
confidence: high
---

# Hermes Agent v0.12.0 版本发布

## 概述

Hermes Agent v0.12.0 发布于 2026 年 4 月 30 日，是紧接 v0.11.0（2026-04-23）之后的迭代版本。v0.11.0 带来了 React/Ink TUI 重写、Transport 架构、QQBot 等重大变化；v0.12.0 的主题是**斜杠指令体系完整化**和**多会话并行支持**。

## v0.12.0 核心变化

### 界面与交互

- **多界面并行**：CLI / Gateway / API Server 可同时运行，不限制单一交互界面
- 完整的斜杠指令体系，包括：
  - `/new` / `/reset` / `/retry` / `/undo` — 会话控制
  - `/copy [N]` / `/image` / `/paste` — 内容操作
  - `/voice` / `/browser` — 特殊模式
  - `/history` / `/save` / `/help` — 信息类

### 会话控制

- 支持**多会话并行管理**
- `/branch` — 分支会话（类似 git 分支）
- `/goal` — 设置持续目标（跨轮次持续工作）
- `/background` — 后台任务

### 配置类命令完善

- `/model [name]` — 切换模型
- `/personality [name]` — 设定人格
- `/reasoning [level]` — 推理深度（none/low/medium/high）
- `/toolsets` — 工具管理
- `/config` — 配置查看

### 安全性

- `/yolo` — 命令审批绕过（适合信任环境）
- `/security` — 安全状态查看
- TUI 审批提示更清晰

## 版本演进（重要背景）

理解 v0.12.0 需要了解前序版本的快速迭代：

| 版本 | 日期 | 重点更新 |
|------|------|---------|
| v0.8.0 | 2026-04-08 | Live Model Switching、后台任务通知、Google AI Studio Provider |
| v0.9.0 | 2026-04-13 | 本地 Web Dashboard、Fast Mode、iMessage/WeChat、Android Termux |
| v0.10.0 | 2026-04-16 | Nous Tool Gateway（搜索、图片、TTS、浏览器自动化） |
| v0.11.0 | 2026-04-23 | React/Ink TUI 重写、Transport 架构、GPT-5.5、**QQBot**、插件系统、/steer |
| v0.12.0 | 2026-04-30 | 斜杠指令完整化、多会话并行、配置选项扩展 |

> **重要**：v0.11.0 是重大架构更新（QQBot、Transport 架构），v0.12.0 是交互完善。

## 与 wiki 中现有页面的关系

- [[hermes-agent]] — Hermes Agent 实体页（概述）
- [[hermes-agent-best-practices]] — 最佳实践
- 当前页面补充了**版本演进时间线**，为最佳实践提供版本参照

## 相关页面

- [[hermes-agent]] — 实体页
- [[hermes-agent-best-practices]] — 最佳实践
- [[openclaw-hermes-comparison]] — 跨框架对比
