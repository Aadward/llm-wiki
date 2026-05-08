---
source_url: https://github.com/hesamsheikh/awesome-openclaw-usecases
ingested: 2026-05-08
sha256: <computed-from-openclaw-cookbook-34kb>
---

# OpenClaw Cookbook Summary

> Source: awesome-openclaw-usecases, 42 real-world use cases
> Last updated: 2026-05-08

## 1. Philosophy & Core Concepts

OpenClaw agents are **persistent, tool-augmented AI assistants** that run continuously. They maintain long-term memory across sessions, can spawn sub-agents for parallel work, connect to messaging platforms as their UI, and run scheduled jobs autonomously.

**Core primitive toolkit:**
- Memory (markdown files, persistent)
- Sessions (`sessions_spawn` / `sessions_send`)
- Skills (ClawHub modules)
- MCP Servers (tool extensions)
- Cron / Heartbeat (autonomous tasks)
- AGENTS.md / SOUL.md / HEARTBEAT.md (config files)

## 2. Foundational Patterns

### Pattern: Messaging Platform as UI
Use Telegram, Discord, WhatsApp as the interface. Multi-agent setups use channel routing — different agents respond to different @mentions.

### Pattern: Zero-Friction Memory Capture
Capture = just text the bot anything. The more you tell it, the more it remembers. Retrieval = semantic search on top.

### Pattern: STATE.yaml Coordination
Shared YAML file instead of orchestrator-based message passing. More scalable, git-auditable.

## 3. Multi-Agent Orchestration

**CEO Pattern:** Main session stays thin — only spawns, routes, summarizes. All execution goes to sub-agents.

**Specialized Team:** 2-4 agents with distinct roles/personality/models, all controllable from one Telegram group. Start with 2, not 4.

**STATE.yaml Race Condition Fix:**
- `AUTONOMOUS.md` — main session only, <50 lines
- `memory/tasks-log.md` — append-only, sub-agents only

**n8n Proxy Pattern:** Agent → webhook → n8n workflow → external API (credentials stay in n8n).

## 4. Memory & Knowledge Systems

- Built-in markdown memory (cumulative, permanent)
- Semantic search via memsearch (vector index on markdown files, hybrid BM25+RRF)
- RAG knowledge base (drop URL → semantic ingest → query)
- Second Brain Dashboard (Next.js, built by agent)

## 5. Scheduled Automation & Cron

Cron is the real product — proactive agents that surface insights without prompting.

**Common schedule structure:**
- Every 15min: task board check
- Every hour: health checks, email triage
- Every 6hrs: knowledge base sync, self health check
- Daily 8AM: morning briefing (weather, calendar, system stats, tasks)
- Weekly: security audits

## 6. Security & Credential Management

**#1 risk:** Agent hardcodes API keys in code.

**Defense layers:**
- TruffleHog pre-push hooks on all repos
- Local-first Git (private Gitea → public GitHub)
- n8n credential isolation
- Branch protection (PR required for main)
- Daily automated security audits
- Build → Test → Lock cycle for n8n workflows

## 7. Infrastructure & DevOps

Agent with SSH + kubectl + terraform + ansible access to home network. Morning briefing includes system health, service status, recent deployments, alerts.

## 8. Content Pipelines

- YouTube Content Pipeline: research → script → thumbnail
- Multi-Agent Content Factory: 3 Discord channels (research/scripts/thumbnails)
- Podcast Production: guest research → outline → show notes → social promo
- AI Video Editing: natural language video manipulation

## 9. Specialized Workflows

- **Pre-Build Idea Validation:** idea-reality-mcp scans GitHub/HN/npm/PyPI before building
- **Market Research → Product Factory:** Last30Days skill mines Reddit/X for pain points
- **Meeting Notes → Task Creation:** transcript → summary → Jira/Linear/Todoist tasks
- **Polymarket Autopilot:** paper trading with strategy patterns

## 10. Anti-Patterns & Pitfalls

1. ❌ Hardcoding secrets → use 1Password CLI or n8n
2. ❌ STATE.yaml race conditions → split AUTONOMOUS.md + tasks-log.md
3. ❌ Growing STATE.yaml unbounded → keep <50 lines, archive completed
4. ❌ Single agent does everything → specialized agents
5. ❌ Skipping n8n lock step → Build → Test → Lock
6. ❌ Starting with 4+ agents → start with 2
7. ❌ Over-engineering before validating output → paste transcript first
8. ❌ Forgetting proactive cron → schedule HEARTBEAT.md

## Key Skills Directory

| Skill | What it does |
|-------|-------------|
| youtube-full | YouTube data + transcripts |
| reddit-readonly | Browse subreddits |
| tech-news-digest | 109+ source tech news |
| tweetclaw | X/Twitter automation |
| knowledge-base | RAG from URLs |
| last30days | Reddit/X pain point mining |

## The OpenClaw Flywheel

```
Capture (text) → Remember (persistent memory) → Research (web/RSS)
     ↓
Plan (goals/tasks) → Execute (sub-agents/cron) → Deliver (Discord/Telegram)
     ↓
Repeat (agent compounds in value over time)
```

---

*Summary of [[openclaw-cookbook-summary]] — original 896-line cookbook at [[openclaw]]*