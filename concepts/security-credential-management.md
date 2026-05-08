---
title: Security & Credential Management
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [security, agent]
sources: [raw/articles/openclaw-cookbook-2026-02.md]
confidence: high
---

# Security & Credential Management

**⚠️ #1 最容易被忽视的风险：Agent 会无条件硬编码 API key。**

## The Hardcoded Secret Problem

> "AI assistants will happily hardcode secrets. They sometimes don't have the same instincts humans do." — Nathan, Self-Healing Home Server

**真实事故：** Day 1 配置 Reef（家庭服务器 Agent）时，API key 被 Agent 无意中内联到代码中并暴露在 commit 里。

## Defense-in-Depth（多层防御）

### Layer 1: TruffleHog Pre-Push Hooks（必须）

```bash
# 安装
brew install trufflesecurity/trufflehog/trufflehog

# 所有仓库添加 pre-push hook
# 阻止任何包含硬编码 API key、token、password 的提交
```

**覆盖范围：** 代码、配置文件、shell 脚本 — 所有文件类型。

### Layer 2: Local-First Git Workflow

```
Private Gitea (staging) → CI scanning → Public GitHub
     ↑                              ↑
  Agent commits here          Human review required
```

- Agent 永远不直接 push 到 public repo
- CI pipeline 在 public push 前扫描
- Human review required before main branch merges

### Layer 3: n8n Credential Isolation（核心模式）

让 Agent 通过 webhook 调用 n8n workflow，凭证留在 n8n：

```
OpenClaw → webhook (no creds) → n8n Workflow (locked, with API keys) → External API
```

**Build → Test → Lock 流程：**
1. **Build** — Agent 创建 workflow
2. **Test** — 验证功能正常
3. **Lock** — 人工锁定 workflow（防止 Agent 后续修改）

### Layer 4: 1Password CLI

永远不把凭证写进代码或环境变量手动管理：

```text
## Infrastructure Agent Access

- 1Password vault (read-only, dedicated AI vault)
- NEVER hardcode secrets — always use: op run -- env | grep API_KEY
```

### Layer 5: Branch Protection

```text
Rules:
- Branch protection: PR required for main
- Agent CANNOT override branch protection
- Read-only access where write isn't needed
```

## Automated Security Audits

```text
Weekly:
- Scan for hardcoded secrets in code
- Check for privileged containers
- Verify overly permissive file/network access
- Check known vulnerabilities in deployed images
```

## Security Checklist Summary

| 措施 | 必须程度 | 作用 |
|------|---------|------|
| TruffleHog pre-push hooks | ⭐⭐⭐ 必须 | 阻止凭证暴露 |
| n8n credential isolation | ⭐⭐⭐ 必须 | 凭证不离开 n8n |
| Local-first Git (Gitea) | ⭐⭐⭐ 必须 | 防止直接暴露到 public |
| 1Password CLI | ⭐⭐ 强烈推荐 | 安全的 secret 访问 |
| Branch protection | ⭐⭐ 强烈推荐 | 防止直接 push main |
| Daily automated security audits | ⭐ 建议 | 持续监控 |

## Related

- [[openclaw]] — platform this security model is built on
- [[multi-agent-orchestration]] — n8n proxy pattern details
- [[scheduled-automation]] — automated security audits in cron schedule
- [[persistent-agent-patterns]] — infrastructure context where SSH access amplifies risk