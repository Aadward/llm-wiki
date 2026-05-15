---
source_url: https://github.com/NVIDIA/NemoClaw
ingested: 2026-05-15
sha256: d8ee9e652a42928a7dd3ee0a5e2e21a92debb5bdef8d1c9ff2e52f0fcbb57308
---

# NVIDIA NemoClaw Source Extract

## Overview
NVIDIA NemoClaw is an open source reference stack that simplifies running OpenClaw always-on assistants more securely.
It installs the NVIDIA OpenShell runtime, part of NVIDIA Agent Toolkit, which provides additional security for running autonomous agents.

**Stars: 20,417 | Fork: 2,663 | Language: TypeScript | License: Apache 2.0 | Status: Alpha**

## Key Facts
- Homepage: https://docs.nvidia.com/nemoclaw/latest/
- GitHub: https://github.com/NVIDIA/NemoClaw
- Alpha software — not production-ready as of March 2026

## Core Capabilities
1. Sandbox OpenClaw (Landlock + seccomp + netns)
2. Inference Routing (agent → inference.local → gateway → provider)
3. Declarative Network Policy (deny-by-default, operator approval)
4. Lifecycle Management (blueprint versioning, digest verification)
5. Messaging Channels (Telegram, Discord, Slack via OpenShell)

## Architecture
- Host CLI (nemoclaw) orchestrates OpenShell
- OpenShell Gateway runs as Docker container with embedded k3s
- Sandbox runs as Kubernetes pod inside gateway container
- L7 proxy injects real credentials at egress (credentials never in sandbox)

## Inference Providers
NVIDIA Endpoints, OpenAI, Anthropic, Google Gemini, Local Ollama, Local vLLM, Local NIM, Model Router, Other OpenAI/Anthropic-compatible endpoints

## Policy Tiers
Restricted (none), Balanced (npm, pypi, huggingface, brew, brave), Open (+ slack, discord, telegram, jira, outlook)

## Sandbox Hardening
- Dropped: CAP_SYS_ADMIN, CAP_SYS_PTRACE, CAP_NET_RAW, CAP_DAC_OVERRIDE, CAP_SYS_CHROOT, CAP_FSETID, CAP_SETFCAP, CAP_MKNOD, CAP_AUDIT_WRITE, CAP_NET_BIND_SERVICE
- ulimit -u 512 (process cap)
- Filesystem: /sandbox, /tmp rw; rest ro
- Landlock kernel requirement: 5.13+
