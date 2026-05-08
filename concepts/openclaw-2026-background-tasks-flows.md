---
title: OpenClaw 2026 后台任务与 Flows 系统
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [agent, automation, infra]
sources: [raw/articles/openclaw-2026-background-tasks-flows.md]
confidence: high
---

# OpenClaw 2026 后台任务与 Flows 系统

## 概述

2026.3.31 引入的**后台任务管理增强**和**任务流控制界面**，是 OpenClaw 从"对话式 Agent"向"持久运行 Agent 系统"迈进的关键一步。

## 后台任务管理（Shared Background Run Control Plane）

### 核心改进

- 将任务转变为**真正的共享后台运行**控制平面
- 统一了 ACP、子代理、cron 和后台 CLI 的执行
- **SQLite 分类账**：路由分离的生命周期更新，支持审计/维护/状态可见性
- **任务感知**：丢失运行自动恢复
- 任务可在 Agent 重启后继续执行

### 生命周期管理

```
任务创建 → 后台运行 → SQLite 记录 → 任务感知 → 丢失恢复
```

## Flows CLI（任务流控制界面）

首个线性任务流控制界面：

```bash
openclaw flows list    # 列出所有运行中的流
openclaw flows show    # 查看特定流详情
openclaw flows cancel  # 取消指定流
```

### 设计原则

- **保持手动多任务流**与**单任务自动同步流**分离
- 为孤立或损坏的流/任务链接提供 `openclaw doctor` 修复

## 与 Scheduled Automation 的关系

[[scheduled-automation]] 的 cron 任务现在可以受益于：
- 统一的 SQLite 任务分类账
- 任务丢失自动恢复
- Flows CLI 可视化监控

## 安全加固（2026.3.31）

### 网关认证
- `trusted-proxy` 拒绝混合共享令牌配置
- 本地直连回退需显式令牌
- 令牌轮换后立即断开活动会话

### 节点命令安全
- 节点命令保持禁用直到配对被批准
- 配对本身不足以暴露节点命令

### 执行安全
- 阻止请求范围环境变量覆盖
- 阻止 Python 包索引变量泄露

### 技能安装安全
- 危险代码 critical 发现默认失败
- 关闭插件安装的技能依赖隐式安装

## 相关概念

- [[scheduled-automation]] — 定时自动化，flows 是其监控界面
- [[persistent-agent-patterns]] — 持久运行能力，任务感知和恢复是其核心
- [[security-credential-management]] — 认证加固与令牌管理
