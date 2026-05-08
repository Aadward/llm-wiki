---
title: Scheduled Automation
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [automation, workflow]
sources: [raw/articles/openclaw-cookbook-2026-02.md]
confidence: high
---

# Scheduled Automation

Cron / Heartbeat 是 OpenClaw 的**真正价值所在** — 当 Agent 主动推送洞察而非被动回答时，价值才最大化。

## HEARTBEAT.md 调度设计模式

```text
## HEARTBEAT.md — Cron Schedule

Every 15 minutes:
- Check kanban board for in-progress tasks → continue work

Every hour:
- Monitor health checks (Gatus, ArgoCD, service endpoints)
- Triage Gmail (label actionable items, archive noise)

Every 6 hours:
- Knowledge base data entry (process new Obsidian notes)
- Self health check (openclaw doctor, disk usage, memory, logs)

Daily:
- 4:00 AM: Nightly brainstorm (explore connections between notes)
- 8:00 AM: Morning briefing (weather, calendars, system stats, task board)

Weekly:
- Knowledge base QA review
- Infrastructure security audit
```

## Morning Briefing Pattern（高价值低摩擦）

固定时间推送日报 — 天气、日历、系统状态、任务面板。一行配置即可搭建。

**模板：**
```text
## Daily Briefing — 8:00 AM

### Weather
- Current conditions and forecast for [location]

### Calendars
- Your events today + partner's events today
- Conflicts or overlaps flagged

### System Health
- CPU / RAM / Storage across all machines
- Services: UP/DOWN status
- Recent deployments, any alerts in last 24h

### Task Board
- Completed yesterday, In progress, Blocked items

### Highlights
- Emails requiring action
- Upcoming deadlines this week
```

## Digest Patterns（信息聚合自动化）

### Reddit Digest
```text
Every day at 5pm, fetch top posts from these subreddits and give digest.
Create memory about what posts I like to see. (e.g., no memes)
```

### YouTube Digest
```text
Every morning at 8am, fetch latest videos from @TED, @Fireship, @lexfridman.
For each new video: get transcript → summarize 2-3 bullets → deliver digest.
```

### Multi-Source Tech News（109+ 来源）
```text
Install tech-news-digest from ClawHub.
Set up daily tech digest at 9am to Discord #tech-news.
Also send to my email.
```

## Self-Healing Pattern

**概念：** 持续健康检查 → 发现问题 → 自动修复 → 日志记录。

```text
Every hour:
- Monitor health checks
- If service DOWN: restart pod / scale resource / fix config
- Log all changes to ~/logs/infra-changes.md

Every 6 hours:
- Self health check: openclaw doctor, disk, memory, logs
```

**核心洞察：** "Agent 可以在你还没发现问题时，就通过 SSH、Terraform、Ansible、kubectl 修复基础设施问题。"

## The Flywheel Effect

```
Scheduled tasks → proactive insights → user asks less → agent does more
                    ↑                                            ↓
                 context compounds                         trust builds
```

Cron 驱动的 Agent 价值随时间**复合增长** — 越久越懂你，越懂你越有价值。

## Related

- [[openclaw]] — platform with built-in cron/heartbeat
- [[security-credential-management]] — self-healing includes security audits
- [[multi-agent-orchestration]] — cron powers multi-agent coordination
- [[memory-knowledge-systems]] — cron syncs and processes memory files