---
title: OpenClaw v2026.3.11 安全强化版本
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [openclaw, release, security, websocket, plugin-architecture, memory-module]
sources: [raw/articles/openclaw-v2026-3-11-release.md]
confidence: high
---

# OpenClaw v2026.3.11 安全强化版本

## 概述

OpenClaw v2026.3.11 发布于 2026 年 3 月 12 日，是一次**不可变版本（Immutable Release）**——所有核心特性均为稳定固定版。此次更新的主题是**全面安全加固**和**跨平台体验优化**。

## 安全更新（重点）

### Gateway/WebSocket 安全强化

强制所有浏览器来源连接进行**源验证**，无论是否存在代理头：
- 从根源阻断可信代理模式下可能的跨站 WebSocket 劫持
- 确保未被信任的跨域无法获得 `operator.admin` 访问权限

### system.run 安全机制

所有基于审批的解释器与运行时命令若无法严格绑定唯一的本地文件操作对象，**自动拒绝执行**，防止运行时命令被外部输入劫持。

### 插件运行安全限制

- 未认证的插件 HTTP 路由**不再继承网关的管理权限**
- `admin` 级方法（如 `sessions.delete`）被完全阻断
- 插件仅在授权范围内执行

### session_status 安全优化

- 强化沙盒会话树的可见性与访问控制
- 防止子代理会话读取或更改父级的元数据或模型配置

### 节点安全增强

节点代理工具**仅限所有者使用**，非所有者无法通过共享工具集触发节点审批。

### 外部内容防护

新增空白分隔的 `EXTERNAL UNTRUSTED CONTENT` 边界标识，防止提示包装绕过标记清理机制。

## 功能变化

### 模型扩展

新增临时模型 **Hunter Alpha** 与 **Healer Alpha**，可在 OpenRouter 内置目录直接调用。

### iOS 端升级

- 主页画布新增欢迎屏幕与实时代理概览
- 浮动控制替换为底部停靠工具栏（小屏适配）

### macOS 端改进

- 模型选择器新增显式思考等级选项（重启后持久保存）
- LaunchAgent 安装路径安全加固

### Ollama 集成扩展

支持**本地与云端+本地两种模式**，可浏览完成注册，通过浏览器登录与精选模型建议。

### Memory 模块增强

- 支持**图像与音频索引**（Gemini `gemini-embedding-2-preview`）
- 维度变化时自动重新索引

### Discord 线程优化

可配置 `autoArchiveDuration`（1小时/1天/3天/1周四种归档时间）。

## 与其他版本的关系

- **v2026.2.23**（2026-02-25）：安全+AI 功能更新（Claude Opus 4.6、Kilo Gateway）
- **v2026.3.11**（2026-03-12）：安全强化为主（本次）
- **v2026.3.13-1**（2026-03-14）：recovery release
- **v2026.3.31**（2026-03-31）：QQ 原生接入
- **v2026.4.5**（2026-04-06）：多媒体生成、任务感知

## 相关页面

- [[openclaw]] — OpenClaw 平台主页
- [[openclaw-2026-plugin-sdk]] — 插件 SDK 重构
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 深度对比
- [[security-credential-management]] — 安全与凭证管理
