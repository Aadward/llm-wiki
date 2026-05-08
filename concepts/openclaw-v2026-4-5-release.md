---
title: OpenClaw v2026.4.5 版本发布
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [openclaw, release, multimedia, image-generation, prompt-cache, chinese-localization]
sources: [raw/articles/openclaw-v2026-4-5-release.md]
confidence: high
---

# OpenClaw v2026.4.5 版本发布

## 概述

OpenClaw v2026.4.5 发布于 2026 年 4 月 6 日，是一次把多条关键能力往前推进的重要版本升级。核心主题是**多媒体生成正式进核心**、**任务进度可见化**和**成本优化**。

## 核心新功能

### 1. 多媒体生成正式进入核心

图片生成和音频合成能力从实验性功能升级为**内置核心能力**：
- `/image` 命令全面可用，支持 GAI/Gemini 等多后端
- 音频/语音合成能力补全
- 生成结果可通过 `/save` 直接存入 workspace

这是 OpenClaw 从纯文本 Agent 向**多模态 Agent** 演进的关键一步。

### 2. /dreaming 从实验走向可用

`/dreaming` 是 OpenClaw 的 AI 创作模式（原用于 AI 画图），v2026.4.5 中已成熟：
- prompt 优化和结果质量大幅提升
- 支持分步预览和迭代优化
- 新增 `--quality` 和 `--style` 参数

### 3. 复杂任务分步进度可见

长时任务（代码生成、数据处理、多步骤工作流）现在能看到具体步骤：
- CLI 新增 `--progress` 标志显示任务步骤
- 每一步有清晰的状态指示器
- 任务可暂停、恢复、取消

### 4. Prompt Cache 更稳定、更省钱

- 改善了与 Claude/GPT 等 provider 的缓存命中率
- 减少重复 token 消耗，降低使用成本
- 缓存策略更智能，自动管理 TTL

### 5. 多语言支持补全

- 控制台（Console）和文档全面支持多语言
- 中文体验优化：中文文档、错误提示本地化

### 6. Anthropic 政策变动正面应对

- 针对 Claude API 使用政策变化做了适配
- 调整了 tool use 行为，确保合规
- 改进了引用（citation）提取逻辑

## 与前版关系

v2026.4.5 是对 v2026.3.11（2026-03-12，安全强化版）的功能补充。两条主线并行：
- **v2026.3.x**：安全加固（SSRF 防护、插件隔离、凭证管理）
- **v2026.4.x**：功能进化（多媒体、任务感知、成本优化）

## 相关页面

- [[openclaw]] — OpenClaw 平台主页
- [[openclaw-2026-plugin-sdk]] — 插件 SDK 重构
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 深度对比
