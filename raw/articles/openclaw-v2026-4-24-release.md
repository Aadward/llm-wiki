---
source_url: https://blog.csdn.net/weixin_48502062/article/details/160563999
ingested: 2026-05-10
sha256: 188542375a9a90723ac2654ab8e5a28b5d46d6527aa9cc767898322d18fb4d64
---

# OpenClaw v2026.4.24 Release Notes

**Source:** CSDN blog (2026-04-24/25)

## Core Update: DeepSeek V4 + Google Meet

OpenClaw v2026.4.24 发布于 2026年4月25日，是一次涵盖核心代理框架、多平台渠道集成、模型生态、浏览器自动化、语音通话、诊断可观测性等数十个模块的超大版本更新。

## Key Changes

### 1. DeepSeek V4 Flash & Pro
- **DeepSeek V4 Flash** 正式取代 Claude Sonnet 成为新用户上手默认模型
- **V4-Pro** 也同步加入内置目录
- 成本直降 17 倍（vs Claude Sonnet）

### 2. Google Meet Native Integration
- 引入 Google Meet 作为**原生捆绑参与者插件**
- AI 可以直接 join 视频会议

### 3. Voice Call → Full Agent
- 语音通话现在可以直达完整智能体
- 不再是简单的语音转文字，而是端到端的 Agent 交互

### 4. Browser Automation Improvements
- 升级浏览器自动化坐标点击与恢复能力
- 更可靠的任务恢复

### 5. Plugin System Rewrite
- 插件系统全面重写
- 性能：1秒 → 43毫秒
- 某些场景：265毫秒 → 8毫秒

### 6. Bug Fixes
- Telegram、Slack、MCP、会话、TTS 多项问题修复

## Version Context
v2026.4.24 follows v2026.4.23 (GPT-5.5). The v2026.4.x series represents OpenClaw's major feature evolution line, complementing the v2026.4.14 security hardening release.
