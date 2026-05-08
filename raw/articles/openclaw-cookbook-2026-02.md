---
source_url: https://github.com/hesamsheikh/awesome-openclaw-usecases
ingested: 2026-05-09
sha256: 7f6b1e095ce03481829ca703c0b0cb06f34ab5c2bddd08b853184b84e0090539
---

# OpenClaw Cookbook: Best Practices from 42 Production Use Cases

> A practical guide to building powerful AI agent workflows with OpenClaw, distilled from real-world experience across 42 community-contributed use cases.
>
> **Source:** [awesome-openclaw-usecases](https://github.com/hesamsheikh/awesome-openclaw-usecases) | **Last Updated:** 2026-02

---

## Table of Contents

1. [Philosophy & Core Concepts](#1-philosophy--core-concepts)
2. [Foundational Patterns](#2-foundational-patterns)
3. [Multi-Agent Orchestration](#3-multi-agent-orchestration)
4. [Memory & Knowledge Systems](#4-memory--knowledge-systems)
5. [Scheduled Automation & Cron](#5-scheduled-automation--cron)
6. [Security & Credential Management](#6-security--credential-management)
7. [Infrastructure & DevOps](#7-infrastructure--devops)
8. [Content Pipelines](#8-content-pipelines)
9. [Specialized Workflows](#9-specialized-workflows)
10. [Anti-Patterns & Pitfalls](#10-anti-patterns--pitfalls)

---

## 1. Philosophy & Core Concepts

### The Agent Mental Model

OpenClaw agents are **persistent, tool-augmented AI assistants** that run continuously and interact through natural language. Unlike a single-prompt AI tool, an OpenClaw agent:

- Maintains **long-term memory** across sessions
- Can **spawn sub-agents** for parallel work
- Connects to **messaging platforms** (Telegram, Discord, WhatsApp) as its UI
- Runs **scheduled jobs** (cron) that work autonomously in the background
- Has **file system and tool access** (SSH, git, APIs, browsers)

> **Core insight from 42 use cases:** The most powerful OpenClaw setups treat the agent not as a chatbot, but as a **24/7 autonomous coworker** that proactively surfaces insights, executes tasks, and coordinates with other agents — all through text-based interfaces.

### OpenClaw's Primitive Toolkit

| Primitive | What It Does |
|-----------|-------------|
| **Memory** | Markdown files that persist across sessions — the agent's long-term memory |
| **Sessions** | Independent agent instances; `sessions_spawn` creates sub-agents, `sessions_send` messages them |
| **Skills** | Reusable capability modules installed from ClawHub or built custom (video editing, arXiv reading, Twitter automation) |
| **MCP Servers** | Model Context Protocol servers that extend the agent's tool access (database queries, API calls, etc.) |
| **Cron / Heartbeat** | Scheduled autonomous tasks that run without user prompting |
| **AGENTS.md / SOUL.md** | Configuration files that define an agent's identity, access scope, and behavioral rules |
| **HEARTBEAT.md** | Cron schedule definition for automated task execution |

### How OpenClaw Agents Are Organized

```
openclaw/
├── AGENTS.md          # Agent identity, access rules, routing logic
├── SOUL.md            # (Optional) Personality and communication style
├── HEARTBEAT.md       # Cron schedule for autonomous tasks
├── memory/            # Persistent markdown memory files
│   ├── goals.md       # Goals and OKRs
│   ├── decisions.md   # Key decisions log (append-only)
│   └── projects/      # Per-project state and notes
├── skills/            # Installed skills (clawhub install)
└── .claud settings   # MCP servers, model config
```

---

## 2. Foundational Patterns

### Pattern 1: Messaging Platform as UI

**The insight:** Don't build a custom UI. Use Telegram, Discord, WhatsApp, or iMessage as the interface your agent exposes. This gives you a polished, mobile-friendly UI for free — and lets you interact with your agent from anywhere.

**How it works:**
- Your agent connects to a messaging platform (Telegram bot is most common)
- You send messages; the agent responds through the same channel
- For multi-agent setups, use **channel routing** — different agents respond to different tags (@milo, @josh, @dev)

**Examples from use cases:**
- [Multi-Agent Specialized Team](#3-multi-agent-orchestration) — 4 agents in one Telegram group, each responding to its own @mention
- [Phone-Based Personal Assistant](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/phone-based-personal-assistant.md) — access via voice call or SMS, hands-free
- [Multi-Channel AI Customer Service](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/multi-channel-customer-service.md) — unify WhatsApp, Instagram, Email into one AI inbox

**Setup (Telegram):**
```text
Install the Telegram skill, connect your bot token.
Then in AGENTS.md:

## Telegram Routing

Group: "Team"
- @milo     → Strategy agent (default, handles untagged messages)
- @josh     → Business analyst
- @marketing → Marketing researcher
- @dev      → Dev agent
- @all      → Broadcast to all
```

### Pattern 2: Zero-Friction Memory Capture

**The insight:** Capture should be as easy as texting a friend. Don't build complex note-taking systems — just let the agent accumulate memories passively and retrieve them on demand.

**The Second Brain pattern:**
```text
// Day-to-day: just text anything
"Hey, remind me to read 'Designing Data-Intensive Applications'"
"Save this link: https://example.com/interesting-article"
"John recommended that restaurant on 5th street"

// Later: retrieve by search
"What did I save about LLM memory systems?"
```

**Key mechanics:**
- OpenClaw's memory system stores everything permanently in markdown files
- The more you tell it, the more it remembers — no organization needed
- For search, layer on **semantic memory search** (see Section 4)

**Pitfall:** OpenClaw's built-in memory has no search — just file storage. As memories grow, you need vector search on top (see [Semantic Memory Search](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/semantic-memory-search.md)).

### Pattern 3: State File Coordination (STATE.yaml)

**The insight:** Instead of passing state through complex message-passing between agents, use a shared YAML file that all agents read/write. This is more scalable, auditable (git), and debuggable than orchestrator patterns.

**Minimal example:**
```yaml
# STATE.yaml — Project coordination
project: website-redesign
updated: 2026-02-10T14:30:00Z

tasks:
  - id: homepage-hero
    status: in_progress
    owner: pm-frontend
    started: 2026-02-10T12:00:00Z
    notes: "Working on responsive layout"
  - id: api-auth
    status: done
    owner: pm-backend
    completed: 2026-02-10T14:00:00Z
    output: "src/api/auth.ts"

next_actions:
  - "pm-content: Resume migration now that api-auth is done"
  - "pm-frontend: Review hero with design team"
```

**Workflow:**
1. Main agent receives task → spawns subagent with specific scope
2. Subagent reads STATE.yaml → finds its assigned tasks
3. Subagent works autonomously → updates STATE.yaml on progress
4. Other agents poll STATE.yaml → pick up unblocked work
5. Main agent checks in periodically → reviews state, adjusts priorities

**Key rule:** Keep STATE.yaml token-light. Archive completed tasks to a separate file. Load STATE.yaml on every heartbeat — if it grows unbounded, you waste tokens every poll.

> **From [Autonomous Project Management](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/autonomous-project-management.md):** "STATE.yaml > orchestrator: File-based coordination scales better than message-passing."

---

## 3. Multi-Agent Orchestration

Multi-agent setups are the most powerful pattern in OpenClaw. The key decisions are: **how many agents**, **how they communicate**, and **who owns what state**.

### Pattern A: The CEO Pattern (Main + Subagents)

The main session stays thin — it only spawns, routes, and summarizes. All execution goes to subagents.

```text
## AGENTS.md — CEO Delegation Pattern

Main session = coordinator ONLY. All execution goes to subagents.

Workflow:
1. New task arrives
2. Check PROJECT_REGISTRY.md for existing PM
3. If PM exists → sessions_send(label="pm-xxx", message="[task]")
4. If new project → sessions_spawn(label="pm-xxx", task="[task]")
5. PM executes, updates STATE.yaml, reports back
6. Main agent summarizes to user

Rules:
- Main session: 0-2 tool calls max (spawn/send only)
- PMs own their STATE.yaml files
- PMs can spawn sub-sub-agents for parallel subtasks
- All state changes committed to git
```

**Skills needed:** `sessions_spawn` / `sessions_send`

### Pattern B: Specialized Team (Multiple Domain Agents)

For solo founders or small teams, run 2-4 specialized agents, each with a distinct role, personality, and model — all controllable from a single Telegram group.

**Example team from [Multi-Agent Specialized Team](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/multi-agent-team.md):**

| Agent | Role | Model | Daily Tasks |
|-------|------|-------|-------------|
| Milo | Strategy lead | Claude Opus | 8AM standup, 6PM recap |
| Josh | Business analyst | Claude Sonnet | 9AM metrics pull |
| Marketing | Content researcher | Gemini | 10AM content ideas, trend monitoring |
| Dev | Coding agent | Claude Opus / Codex | CI/CD health, PR review |

**Shared memory structure:**
```
team/
├── GOALS.md           # Current OKRs (all agents read)
├── DECISIONS.md       # Key decisions log (append-only)
├── PROJECT_STATUS.md  # Current state (updated by all)
└── agents/
    ├── milo/          # Milo's private notes
    ├── josh/          # Josh's private notes
    └── ...
```

**Telegram routing:**
```text
## AGENTS.md — Telegram Routing

Group: "Team"
- @milo       → Strategy agent
- @josh       → Business agent
- @marketing  → Marketing agent
- @dev        → Dev agent
- @all        → Broadcast to all
- No tag      → Milo (team lead) handles by default
```

**Key insight:** "Start with 2, not 4. Begin with a lead + one specialist, then add agents as you identify bottlenecks." — from the original use case.

### Pattern C: Chained Content Pipeline

Agents in sequence, where each agent's output feeds the next. Fully automated, runs on schedule.

```
Research Agent (#research channel)
    ↓ feeds
Writing Agent (#scripts channel)  
    ↓ feeds
Thumbnail Agent (#thumbnails channel)
```

**Setup prompt:**
```text
Build me a content factory inside Discord:
1. Research Agent (#research): Every morning at 8 AM, research top trending
   stories. Post the top 5 content opportunities.
2. Writing Agent (#scripts): Take the best idea from the research agent
   and write a full script/thread draft. Post it in #scripts.
3. Thumbnail Agent (#thumbnails): Generate AI thumbnails for the content.
   Post them in #thumbnails.
Run this pipeline automatically every morning.
```

### Pattern D: State File Split — Avoiding Race Conditions

⚠️ **Critical pitfall** when running multi-agent with sub-agents writing to shared files.

**Problem:** Both the main session and spawned sub-agents may try to update the same task file. OpenClaw's `edit` tool requires exact `oldText` match — if content changed between read and write, the edit silently fails.

**The fix — split into two files:**
1. **`AUTONOMOUS.md`** — goals + open backlog only. **Only the main session touches this.** Sub-agents never edit it. Keep it under ~50 lines (loaded on every heartbeat).
2. **`memory/tasks-log.md`** — append-only log. Sub-agents **only append at the bottom**, never edit existing lines.

```markdown
# tasks-log.md — Completed Tasks (append-only)
# Sub-agents: always append to the END. Never edit existing lines.

### 2026-02-24
- ✅ TASK-001: Research competitors → research/competitors.md
- ✅ TASK-002: Draft blog post → drafts/post-1.md
```

**Rule to give your sub-agents:**
> "When done, append a ✅ line to `memory/tasks-log.md`. Never edit `AUTONOMOUS.md` directly."

This pattern is borrowed from Git's commit log — you never rewrite history, you only add new commits.

### Pattern E: n8n Proxy — Credential Isolation

**When to use:** For any external API interaction (Slack, email, CRM), delegate to n8n workflows via webhooks. OpenClaw never touches credentials.

```
┌──────────────┐     webhook call      ┌─────────────────┐     API call     ┌──────────────┐
│   OpenClaw   │ ───────────────────→  │   n8n Workflow   │ ─────────────→  │  External    │
│   (agent)    │   (no credentials)    │  (locked, with   │  (credentials   │  Service     │
│              │                       │   API keys)      │   stay here)    │  (Slack, etc)│
└──────────────┘                       └─────────────────┘                  └──────────────┘
```

**Workflow:**
1. Agent designs and creates the n8n workflow via API (with webhook trigger)
2. User adds credentials manually in n8n UI
3. User **locks** the workflow (prevents agent from modifying it)
4. Agent calls the webhook URL with JSON payload — it never sees the API key

**Why this matters:** "AI assistants will happily hardcode secrets. They sometimes don't have the same instincts humans do." — [Self-Healing Home Server](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/self-healing-home-server.md)

---

## 4. Memory & Knowledge Systems

### Pattern 1: Built-in Markdown Memory (Zero-Friction)

OpenClaw's native memory is markdown files in the `memory/` directory. It's cumulative and permanent — everything you've told the agent is stored.

```text
// Simple capture — just text the bot
"Remember: Sarah from marketing wants weekly reports on Fridays"
"Key decision: we picked PostgreSQL over MongoDB because of ACID compliance"
```

**What works:**
- Brain dumps of goals, preferences, context
- Storing decisions and reasoning
- Notes about people, projects, preferences

**What doesn't work:** Searchable retrieval as memory grows. Needs a search layer for large memories.

### Pattern 2: Semantic Memory Search (Vector Index on Markdown Files)

**Tool:** [memsearch](https://github.com/zilliztech/memsearch) — adds vector-powered search on top of OpenClaw's markdown memory files.

**Setup:**
```bash
pip install memsearch
memsearch config init
memsearch index ~/path/to/memory/       # One-time indexing
memsearch watch ~/path/to/memory/        # Auto-reindex on changes
```

**Key features:**
- SHA-256 content hashing → unchanged files never re-embedded (saves API costs)
- Hybrid search (dense vectors + BM25 full-text) with RRF reranking
- Works with OpenAI, Google, Voyage, Ollama, or fully local (no API key)
- Markdown stays source of truth — vector index is a derived cache

**Search example:**
```bash
memsearch search "what caching solution did we pick?"
```

### Pattern 3: RAG Knowledge Base from URLs

**Skills:** [knowledge-base](https://clawhub.ai) skill + `web_fetch`

**How it works:**
1. Drop any URL (article, tweet, YouTube, PDF) into a Telegram topic or Slack channel
2. Agent fetches content, chunks it, stores with metadata (title, URL, date, type)
3. Query semantically: "What do I have about agent memory systems?"

```text
When I drop a URL in the "knowledge-base" topic:
1. Fetch the content (article, tweet, YouTube transcript, PDF)
2. Ingest it into the knowledge base with metadata
3. Reply with confirmation: what was ingested and chunk count

When I ask a question in this topic:
1. Search the knowledge base semantically
2. Return top results with sources and relevant excerpts
```

### Pattern 4: Second Brain Dashboard

**Concept:** Text anything → bot remembers → custom Next.js dashboard for retrieval.

**Setup:**
```text
I want to build a second brain system where I can review all our notes,
conversations, and memories. Please build that out with Next.js.

Include:
- A searchable list of all memories and conversations
- Global search (Cmd+K) across everything
- Ability to filter by date and type
- Clean, minimal UI
```

OpenClaw builds and deploys the entire Next.js app. The key insight: **capture is texting, retrieval is searching** — no folders, no tags, no complexity.

---

## 5. Scheduled Automation & Cron

Cron jobs are the **flywheel** of OpenClaw — the real value emerges when agents proactively surface insights without being asked.

### The Cron Schedule Design Pattern

**Structure from [Self-Healing Home Server](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/self-healing-home-server.md):**

```text
## HEARTBEAT.md — Cron Schedule

Every 15 minutes:
- Check kanban board for in-progress tasks → continue work

Every hour:
- Monitor health checks (Gatus, ArgoCD, service endpoints)
- Triage Gmail (label actionable items, archive noise)
- Check for unanswered alerts or notifications

Every 6 hours:
- Knowledge base data entry (process new Obsidian notes)
- Self health check (openclaw doctor, disk usage, memory, logs)

Every 12 hours:
- Code quality and documentation audit
- Log analysis via monitoring stack

Daily:
- 4:00 AM: Nightly brainstorm (explore connections between notes)
- 8:00 AM: Morning briefing (weather, calendars, system stats, task board)

Weekly:
- Knowledge base QA review
- Infrastructure security audit
```

### Morning Briefing Pattern

A daily digest delivered at a fixed time — weather, calendar, system health, task board. One of the highest-value, lowest-effort patterns.

**Template:**
```text
## Daily Briefing Format — Generate at 8:00 AM:

### Weather
- Current conditions and forecast for [location]

### Calendars
- Your events today
- Partner's events today
- Conflicts or overlaps flagged

### System Health
- CPU / RAM / Storage across all machines
- Services: UP/DOWN status
- Any alerts in last 24h

### Task Board
- Cards completed yesterday
- Cards in progress
- Blocked items needing attention

### Highlights
- Emails requiring action
- Upcoming deadlines this week
```

### Digest Patterns

**Daily Reddit Digest:**
```text
Install the reddit-readonly skill, then:
"I want you to give me the top performing posts from these subreddits:
<list>. Every day at 5pm, run this process and give me the digest.
Create a memory for the reddit processes about the type of posts I like."
```

**Daily YouTube Digest:**
```text
Every morning at 8am, fetch the latest videos from these YouTube channels
and give me a digest with key insights from each: @TED, @Fireship, @lexfridman.
For each new video (uploaded in last 24-48h): get transcript, summarize in
2-3 bullets, include title and link. Save my channel list to memory."
```

**Multi-Source Tech News (109+ sources):**
```text
Install tech-news-digest from ClawHub. Set up a daily tech digest at 9am
to Discord #tech-news channel. Also send it to my email.
```

### The Self-Healing Pattern

**Concept:** Run health checks continuously → detect issues → auto-fix → log changes.

```text
Every hour:
- Monitor health checks (Gatus, ArgoCD, service endpoints)
- If service is DOWN: restart pod, scale resource, or fix config
- Log all infrastructure changes to ~/logs/infra-changes.md

Every 6 hours:
- Self health check: openclaw doctor, disk usage, memory, logs
```

**Key insight:** "The agent can run SSH, Terraform, Ansible, and kubectl commands to fix infrastructure issues before you even know there's a problem."

---

## 6. Security & Credential Management

Security is the **#1 overlooked concern** in OpenClaw setups. The agent will happily hardcode an API key in a script if you don't enforce guardrails.

### The Hardcoded Secret Problem

> "AI assistants will happily hardcode secrets. They sometimes don't have the same instincts humans do." — Nathan, [Self-Healing Home Server](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/self-healing-home-server.md)

**Real incident:** Day 1 of setting up Reef (a home server agent), Nathan accidentally exposed an API key in a commit. The agent had put it inline in the code without being asked.

### Defense-in-Depth Checklist

**1. TruffleHog pre-push hooks (mandatory):**
```bash
# Install TruffleHog
brew install trufflesecurity/trufflehog/trufflehog

# Add pre-push hook to ALL repos
# Blocks any commit containing hardcoded API keys, tokens, or passwords
```

**2. Local-first Git workflow:**
```text
## Security Checklist

1. Pre-push hooks:
   - Install TruffleHog on ALL repositories
   - Block any commit containing hardcoded API keys

2. Local-first Git:
   - Use Gitea (self-hosted) for private code before pushing to public GitHub
   - CI scanning pipeline runs before any public push
   - Human review required before main branch merges

3. Agent constraints:
   - Branch protection: PR required for main, agent cannot override
   - Read-only access where write isn't needed
   - All changes logged and auditable via git
```

**3. Credential isolation with n8n:**
- Never give the agent API keys directly
- Use n8n webhooks as a proxy (see Section 3, Pattern E)
- n8n credentials stay in n8n's credential store

**4. Dedicated secrets vault:**
```text
## Infrastructure Agent

Access:
- 1Password vault (read-only for credentials, dedicated AI vault)
- NEVER hardcode secrets — always use 1Password CLI or environment variables
- Read-only access where write isn't needed
```

**5. Daily automated security audits:**
```text
Weekly:
- Scan for hardcoded secrets in code
- Check for privileged containers
- Verify overly permissive file/network access
- Check known vulnerabilities in deployed images
```

### The Build → Test → Lock Cycle

For n8n workflows and any automation that touches external services:

1. **Build** — Agent creates the workflow
2. **Test** — Verify it works correctly
3. **Lock** — Prevent the agent from modifying the workflow after it's tested

Without locking, the agent can silently modify how it interacts with APIs — defeating the security model.

---

## 7. Infrastructure & DevOps

### Self-Healing Home Server Setup

**Concept:** Agent with SSH access to home network, Kubernetes cluster, and infrastructure-as-code tools.

**Agent configuration:**
```text
## Infrastructure Agent — "Reef"

Access:
- SSH to all machines on home network (192.168.1.0/24)
- kubectl for K3s cluster
- 1Password vault (dedicated AI vault, read-only)
- Gmail via gog CLI
- Calendar (yours + partner's)
- Obsidian vault at ~/Documents/Obsidian/

Rules:
- NEVER hardcode secrets — always use 1Password CLI
- NEVER push directly to main — always create a PR
- Run openclaw doctor as part of self-health checks
- Log all infrastructure changes to ~/logs/infra-changes.md
```

**Tools the agent uses:**
- `ssh` — remote machine access
- `kubectl` — Kubernetes cluster management
- `terraform` / `ansible` — infrastructure-as-code
- `1password` CLI — secrets management
- `gog` CLI — email access
- `openclaw doctor` — self-diagnostics

### Morning Infrastructure Briefing

```text
### System Health (from [Self-Healing Home Server])
- CPU / RAM / Storage across all machines
- Services: UP/DOWN status
- Recent deployments (ArgoCD)
- Any alerts in last 24h
```

### Git-Based Audit Trail

All infrastructure changes are committed to git with meaningful messages. This gives you:
- Full history of what changed and when
- Diff review before any change goes live
- Ability to roll back

```text
# In practice:
1. Agent proposes a change → commits to a branch
2. You review the diff
3. You merge (or the agent merges after your approval)
4. CI/CD picks up the change and applies it
```

---

## 8. Content Pipelines

### YouTube Content Pipeline

**Goal:** Automate video idea scouting, research, and tracking.

```
1. Research Agent: Scans trending topics, competitor content, social media
   → outputs top 5 content opportunities with sources

2. For each selected topic:
   - Fetch YouTube transcripts (via TranscriptAPI.com, not yt-dlp)
   - Summarize main points
   - Identify gaps or angles competitors missed

3. Track in a content pipeline file:
   - Status: Idea → Research → Script → Record → Edit → Publish
   - Notes per stage
```

**Why TranscriptAPI over yt-dlp:**
| | yt-dlp | TranscriptAPI |
|--|--------|--------------|
| Logs | Verbose, floods context | Clean JSON responses |
| Cloud | Doesn't work on GCP | Works everywhere |
| Reliability | Gets blocked by YouTube randomly | Serving millions, cached |

### Multi-Agent Content Factory (Discord)

**Setup from [Content Factory](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/content-factory.md):**

Discord server with three channels:
- `#research` — Research Agent posts trending opportunities
- `#scripts` — Writing Agent drafts scripts/threads
- `#thumbnails` — Thumbnail Agent generates cover images

**Daily run (8 AM):**
1. Research Agent scans → posts top 5 to `#research`
2. Writing Agent picks best → drafts content → posts to `#scripts`
3. Thumbnail Agent generates images → posts to `#thumbnails`
4. You wake up to finished content ready for review

### Podcast Production Pipeline

**Full workflow from [Podcast Production Pipeline](https://github.com/hesamsheikh/awesome-openclaw-usecases/blob/main/usecases/podcast-production-pipeline.md):**
1. Guest research — background, past appearances, relevant topics
2. Episode outline — structured with talking points
3. Show notes — timestamped summary for show notes / blog
4. Social promo — tweet threads, LinkedIn posts, newsletter copy

### AI Video Editing via Chat

**Skill:** video-editor-ai

Edit videos by describing changes in natural language — trim, merge, add music, subtitles, color grade, crop to vertical. No timeline, no GUI.

```
"Trim the first 30 seconds"
"Add the song 'uplifting-piano.mp3' starting at 1:20"
"Add subtitles using the transcript"
"Convert to 9:16 vertical for TikTok"
```

---

## 9. Specialized Workflows

### Pre-Build Idea Validation

**Tool:** [idea-reality-mcp](https://github.com/mnemox-ai/idea-reality-mcp) — MCP server that scans GitHub, HN, npm, PyPI, Product Hunt before building.

**Integration:**
```text
Before starting any new project, feature, or tool, always run idea_check first.

Rules:
- If reality_signal > 70: STOP. Report top 3 competitors with star counts.
  Ask me if I want to proceed, pivot, or abandon.
- If reality_signal 30-70: Show results and pivot_hints.
  Suggest a niche angle that existing projects don't cover.
- If reality_signal < 30: Proceed to build.
  Mention that the space is open.
- Always show reality_signal score and top competitors before writing code.
```

**Example output:**
```
reality_signal: 90/100 (very crowded)
Top competitors:
1. Gitea — 53,940 stars
2. reviewdog — 9,104 stars
3. Danger — 5,649 stars
→ Pivot suggestions: Focus on Rust-only, or target financial code compliance
```

### Market Research → Product Factory

**Tools:** [Last 30 Days](https://github.com/mvanhorn/last30days-skill/) skill — mines Reddit and X for real pain points.

**Pipeline:**
```text
# Step 1: Research pain points
"Use the Last 30 Days skill to research challenges people are having
with [topic]. Organize into: top pain points, specific complaints,
gaps in existing solutions, product opportunities."

# Step 2: Build MVP
"Build me an MVP that solves [specific pain point from research].
Keep it simple — just the core functionality. Ship as a web app."

# Step 3: Validate demand
"Every Monday, use Last 30 Days to research what people are saying
about [your niche]. Summarize top opportunities and send to Telegram."
```

### Automated Meeting Notes → Task Creation

**Pipeline:**
1. Paste / upload meeting transcript (Otter.ai, Zoom, or manual)
2. Agent extracts: decisions, discussion topics, action items with owners + deadlines
3. Agent creates tasks in Jira / Linear / Todoist — assigned to the right person
4. Agent posts summary to Slack / Discord
5. Scheduled reminders ping assignees before deadlines

```text
When I paste a meeting transcript:
1. Write a concise summary (max 5 bullet points) covering key decisions.
2. Extract ALL action items with: what, who, deadline (if mentioned, else TBD).
3. Create a Jira ticket for each action item, assigned to the right person.
4. Post the full summary to #meeting-notes in Slack.
```

### Polymarket Autopilot

**Paper trading on prediction markets — no real money.**

```text
You are a Polymarket paper trading autopilot. Run continuously (cron every 15 min):

Strategies:
- TAIL: Follow strong trends (>60% probability + volume spike)
- BONDING: Contrarian on overreactions (sudden drops >10%)
- SPREAD: Arbitrage when YES+NO > 1.05

Every morning at 8 AM, post to Discord:
- Yesterday's trades (entry/exit prices, P&L)
- Current portfolio value and open positions
- Win rate and strategy performance
- Market insights and recommendations

Never use real money. Paper trading only.
```

### Family Calendar & Household Assistant

**Aggregates all family calendars** into a morning briefing, monitors messages for appointments, manages household inventory.

```text
Morning briefing at 7:30 AM (before everyone wakes up):
- Weather for the day
- Your calendar + partner's calendar (today + tomorrow)
- Kids' school schedule
- Any conflicts or overlaps flagged
- Reminders: "Sarah has piano at 4pm, dinner at 6pm"
```

---

## 10. Anti-Patterns & Pitfalls

### ❌ Hardcoding Secrets in Code

**Problem:** The agent will put API keys inline if you don't enforce guardrails.

**Solutions:**
- Use 1Password CLI: `op run -- env | grep API_KEY`
- Use n8n webhooks (credential isolation)
- TruffleHog pre-push hooks on every repo
- Never let agent push directly to main

### ❌ STATE.yaml Race Conditions

**Problem:** Multiple sub-agents editing the same file simultaneously causes silent failures.

**Solution:** Split into two files (see Section 3, Pattern D):
- `AUTONOMOUS.md` — main session only, small (~50 lines)
- `memory/tasks-log.md` — append-only, sub-agents only

### ❌ Growing STATE.yaml Unbounded

**Problem:** STATE.yaml gets loaded on every heartbeat. If it grows with completed tasks, you waste tokens constantly.

**Solution:**
- Keep `AUTONOMOUS.md` under ~50 lines (goals + open backlog only)
- Archive completed tasks to a separate file (only read on-demand)
- Use `tasks-log.md` pattern for completed work

### ❌ Single Agent Does Everything

**Problem:** A single agent's context fills up fast when juggling strategy, code, marketing research, and business analysis.

**Solution:** Use specialized agents with distinct roles and models. Match model capability to task complexity — don't use an expensive reasoning model for keyword monitoring.

### ❌ Skipping the Lock Step in n8n Workflows

**Problem:** Without locking workflows after testing, the agent can silently modify how it interacts with APIs — defeating the security model.

**Solution:** Build → Test → Lock. Once a workflow is proven, lock it.

### ❌ Starting with 4+ Agents

**Problem:** Multi-agent setups are complex. Too many agents too early leads to confusion about who owns what.

**Solution:** Start with 2 agents (lead + one specialist). Add agents only when you identify specific bottlenecks.

### ❌ Over-Engineering Pipelines Before Validating Output

**Problem:** Building elaborate automated pipelines before confirming the agent produces good output.

**Solution:** Start simple — paste a transcript, get a summary. If the output is good, automate incrementally. Don't build a folder watch + API integration before validating summary quality.

### ❌ Forgetting to Schedule Proactive Tasks

**Problem:** Treating the agent as purely reactive — only works when you ask it things.

**Solution:** Define a cron schedule in HEARTBEAT.md. The real value of OpenClaw is **proactive** agents that surface insights without prompting.

---

## Quick Reference: Skills Directory

| Skill | What It Does | Link |
|-------|-------------|------|
| `youtube-full` | YouTube channel/video data + transcripts | clawhub.ai |
| `reddit-readonly` | Browse subreddits, search, pull threads | clawhub.ai |
| `tech-news-digest` | Multi-source tech news aggregation (109+ sources) | clawhub.ai |
| `tweetclaw` | Full X/Twitter automation | npm: @xquik/tweetclaw |
| `knowledge-base` | RAG from URLs, tweets, articles | clawhub.ai |
| `x-research-v2` | Social media research | clawhub.ai |
| `gog` | Gmail access | clawhub.ai |
| `1password` | Secrets management | CLI |
| `last30days` | Mine Reddit/X for pain points | GitHub |

## Quick Reference: MCP Servers

| MCP Server | What It Does |
|-----------|-------------|
| `idea-reality-mcp` | Pre-build competition validation across GitHub, HN, npm, PyPI, Product Hunt |
| `memsearch` | Vector semantic search on markdown memory files |

---

## Summary: The OpenClaw Flywheel

The most successful OpenClaw setups create a **flywheel**:

```
1. Capture    → Text/messaging = zero friction memory input
2. Remember    → Persistent memory across sessions
3. Research    → Web fetch, RSS, social media, GitHub monitoring
4. Plan        → Goal-driven task generation, STATE.yaml tracking
5. Execute     → Sub-agents, cron jobs, skills, MCP tools
6. Deliver     → Discord/Telegram summaries, dashboards, reports
7. Repeat      → Every morning, the agent is smarter than the last
```

The agents that deliver the most value are the ones that **proactively work overnight**, **surface insights before you ask**, and **compound in usefulness over time** as they accumulate context about your life and work.

---

*Cookbook compiled from [awesome-openclaw-usecases](https://github.com/hesamsheikh/awesome-openclaw-usecases) — 42 real-world use cases from the OpenClaw community.*