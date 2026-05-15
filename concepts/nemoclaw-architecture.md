---
title: NemoClaw 架构深度解析
created: 2026-05-15
updated: 2026-05-15
type: concept
tags: [agent, security, platform]
sources: [raw/articles/nvidia-nemoclaw-2026.md]
confidence: high
---

# NemoClaw 架构深度解析

## 设计背景

OpenClaw 是通用的自主 Agent 运行时——Agent 可以做任何事：任意网络请求、访问主机文件系统、调用任意推理端点。**无安全边界**是其在企业/云端部署的核心障碍。

NemoClaw 的设计目标：在不修改 OpenClaw 本身的前提下，通过外层沙箱 + gateway 路由 + 声明式策略，让 OpenClaw 助手可以安全地始终在线运行。

## 三层组件模型

```
┌─────────────────────────────────────────────────────────┐
│  Host Machine (Linux / macOS / WSL2 / DGX Spark)      │
│                                                         │
│  ┌─ nemoclaw CLI (Node.js) ──────────────────────┐   │
│  │  onboard / connect / status / logs / rebuild   │   │
│  └───────────────────────────────────────────────┘   │
│           │                                           │
│           ▼                                           │
│  ┌─ Docker daemon ───────────────────────────────┐   │
│  │  ┌─ OpenShell Gateway Container ─────────────┐  │   │
│  │  │  ┌─ L7 Proxy ──────────────────────────┐  │  │   │
│  │  │  │  Authorization header rewrite      │  │  │   │
│  │  │  │  Credential injection at egress      │  │  │   │
│  │  │  └─────────────────────────────────────┘  │  │   │
│  │  │                                           │  │   │
│  │  │  ┌─ k3s cluster ──────────────────────┐  │  │   │
│  │  │  │  ┌─ Sandbox Pod 🔒 ───────────────┐  │  │  │   │
│  │  │  │  │  Landlock + seccomp + netns  │  │  │  │   │
│  │  │  │  │  OpenClaw agent               │  │  │  │   │
│  │  │  │  │  NemoClaw plugin              │  │  │  │   │
│  │  │  │  └──────────────────────────────┘  │  │  │   │
│  │  │  └─────────────────────────────────────┘  │  │   │
│  │  └───────────────────────────────────────────┘  │   │
│  └────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
           │           │           │
           ▼           ▼           ▼
    ┌──────────┐ ┌──────────┐ ┌──────────┐
    │ NVIDIA   │ │ OpenAI / │ │ Telegram │
    │Endpoints │ │Anthropic │ │ Discord  │
    └──────────┘ └──────────┘ └──────────┘
```

## 组件职责

| 组件 | 位置 | 职责 |
|---|---|---|
| `nemoclaw` CLI | Host 进程 | 编排 OpenShell、引导 onboarding、配置管理 |
| OpenShell Gateway | Docker 容器 | 凭证存储、L7 代理、策略引擎、设备认证 |
| k3s cluster | Gateway 内 | Kubernetes 控制面，调度沙箱 Pod |
| L7 Proxy | Gateway 内 | 拦截 egress，注入真实凭证 |
| Sandbox Pod | k3s Pod | 运行 OpenClaw + NemoClaw plugin |
| NemoClaw Plugin | 沙箱内 | 注册 provider 元数据、`/nemoclaw` 命令、运行时上下文注入 |

## 关键设计原则

### 凭证永远不在沙箱内

```
Agent (sandbox)  ──inference.local──►  L7 Proxy (gateway)  ──real key──►  Provider
                           ▲
                    placeholder token
                    (no real credential)
```

Agent 持有 placeholder（如 `nvapi-...` 前缀的假 key），L7 Proxy 在出口处替换为真实凭证。

### Blueprint 版本化

Blueprint 是版本化的 YAML 包，定义了：
- 沙箱镜像内容
- 网络策略
- 推理 profile

Runner 在应用前执行摘要验证，确保供应链安全。

### 推理路由

Agent 只访问 `inference.local`，Provider 变更对 Agent 透明。沙箱重建后自动使用新 Provider 配置。

## 与 OpenClaw 的边界

> **重要**：NemoClaw 管理环境下，永远用 `nemoclaw onboard` 管理沙箱生命周期。
> - ❌ `openshell self-update`
> - ❌ `openshell gateway start --recreate`
> - ❌ `openshell sandbox create`
> - ✅ `nemoclaw onboard`（唯一入口）

## 部署拓扑变体

| 平台 | 容器运行时 | 备注 |
|---|---|---|
| Linux | Docker | 主测试路径 |
| macOS (Apple Silicon) | Colima / Docker Desktop | 需 Xcode CLT |
| DGX Spark | Docker | 参照 NVIDIA Spark playbook |
| Windows WSL2 | Docker Desktop (WSL backend) | 需 WSL2 |

## 相关页面

- [[nemoclaw|NemoClaw 实体页]]
- [[openclaw|OpenClaw 通用运行时]]
- [[openclaw-gateway-architecture|OpenClaw Gateway 架构]]
- [[security-model-comparison|安全模型对比]]
