---
title: NemoClaw
created: 2026-05-15
updated: 2026-05-15
type: entity
tags: [agent, security, platform]
sources: [raw/articles/nvidia-nemoclaw-2026.md]
confidence: high
---

# NemoClaw

## 概述

**NVIDIA NemoClaw** 是运行在 [OpenShell](./openclaw.md) 沙箱中的 OpenClaw 安全加固参考堆栈。解决 OpenClaw 作为通用 Agent 运行时「无安全边界」的核心问题——让始终在线的 AI 助手可以安全地跑在云端、边缘 GPU 或本地设备上，而无需担心凭证泄露、无限制网络访问或文件系统污染。

| 属性 | 值 |
|---|---|
| Stars | 20,417 ⭐ |
| Fork | 2,663 |
| 语言 | TypeScript |
| License | Apache 2.0 |
| 状态 | **Alpha**（非生产就绪） |
| 官方文档 | https://docs.nvidia.com/nemoclaw/latest/ |
| GitHub | https://github.com/NVIDIA/NemoClaw |

## 核心能力

1. **沙箱化执行** — Landlock + seccomp + network namespace 三层隔离，deny-by-default
2. **推理路由** — Agent 只访问 `inference.local`，凭证永远留在宿主机
3. **声明式网络策略** — YAML 定义 egress 规则，未列明目标需操作员审批
4. **生命周期管理** — Blueprint 版本化 + 摘要验证，rebuild 保留 workspace
5. **消息通道集成** — Telegram / Discord / Slack 经 gateway 接入沙箱

## 与 OpenClaw 的关系

> OpenClaw = 通用 Agent 运行时（无安全边界）
> NemoClaw = OpenClaw + OpenShell 沙箱 + 策略控制 + 凭证管理

OpenClaw 本身可以"做任何事"——任意网络请求、访问主机文件系统、调用任意推理端点。NemoClaw 在其外面套了一层可观测、可控的安全壳。

参见：[[openclaw|OpenClaw 实体页]] ｜ [[openclaw-gateway-architecture|OpenClaw Gateway 架构]]

## 支持的推理 Provider

| Provider | 类型 | 说明 |
|---|---|---|
| NVIDIA Endpoints | OpenAI-compatible | build.nvidia.com 托管模型（Nemotron 3 Super 120B 等） |
| OpenAI | Native | 原生 OpenAI Responses / Chat Completions API |
| Anthropic | Native | Anthropic Messages API |
| Google Gemini | OpenAI-compatible | Google 的 OpenAI-compatible 端点 |
| Local Ollama | Local | localhost:11434，需本地安装 Ollama |
| Local vLLM | Local（实验性） | localhost:8000 |
| Local NIM | Local（实验性） | NVIDIA NIM 容器 |
| Model Router | 路由 | NVIDIA LLM Router v3，按 query 成本-质量自动路由 |
| 其他兼容端点 | OpenAI / Anthropic compatible | OpenRouter、LocalAI、SGLang、vLLM 等 |

凭证存储在宿主机，沙箱只持有 placeholder token。L7 代理在 egress 点注入真实凭证。

## 安装与 Onboard

```bash
curl -fsSL https://www.nvidia.com/nemoclaw.sh | bash
```

安装后自动触发 `nemoclaw onboard` 交互向导，完成：
- 推理 provider 和模型选择（+ API key 验证）
- 策略层级选择（Restricted / Balanced / Open）
- 消息通道配置（可选）
- 沙箱 + Gateway 创建

非交互式：
```bash
NEMOCLAW_NON_INTERACTIVE=1 NEMOCLAW_ACCEPT_THIRD_PARTY_SOFTWARE=1 \
NEMOCLAW_PROVIDER=routed NEMOCLAW_POLICY_TIER=open \
nemoclaw onboard --non-interactive
```

## 常用 CLI

```bash
nemoclaw <name> connect     # 连接沙箱 TUI
nemoclaw <name> status      # 健康检查
nemoclaw <name> logs        # 查看日志
nemoclaw <name> rebuild     # 重建（保留 workspace）
nemoclaw inference set       # 运行时切换模型
nemoclaw <name> policy-add  # 动态添加策略 preset
nemoclaw <name> snapshot create  # 快照
nemoclaw update             # 升级 CLI + 沙箱
nemoclaw uninstall          # 卸载
```

## 生态资源

- **awesome-nemoclaw** — 社区 preset/配方合集：https://github.com/VoltAgent/awesome-nemoclaw
- 社区 preset：GitLab、Notion、Linear、Confluence、Teams、Zendesk、Sentry、Stripe、Cloudflare、Google Workspace、AWS、GCP、Vercel、Supabase 等

## 相关页面

- [[openclaw|OpenClaw]] — 通用 Agent 运行时
- [[openclaw-gateway-architecture|OpenClaw Gateway 架构]] — 路由层设计
- [[security-model-comparison|安全模型对比]] — NemoClaw vs Hermes 安全边界对比
- [[persistent-agent-patterns|持久 Agent 模式]] — 沙箱与持久 Agent 的结合
- [[scheduled-automation|定时自动化]] — NemoClaw 定时任务设计
