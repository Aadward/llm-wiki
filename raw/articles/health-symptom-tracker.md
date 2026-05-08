---
source_url: https://github.com/hesamsheikh/awesome-openclaw-usecases
ingested: 2026-05-09
sha256: 84c9b1021176f6f9b5d68f093f92699a8432c0e50b48743e452d2d979a6c1c13
---

# Health & Symptom Tracker

Identifying food sensitivities requires consistent logging over time, which is tedious to maintain. You need reminders to log and analysis to spot patterns.

This workflow tracks food and symptoms automatically:

• Message your food and symptoms in a dedicated Telegram topic and OpenClaw logs everything with timestamps
• 3x daily reminders (morning, midday, evening) prompt you to log meals
• Over time, analyzes patterns to identify potential triggers

## Skills you Need

- Cron jobs for reminders
- Telegram topic for logging
- File storage (markdown log file)

## How to Set it Up

1. Create a Telegram topic called "health-tracker" (or similar).
2. Create a log file: `~/clawd/memory/health-log.md`
3. Prompt OpenClaw:
```text
When I message in the "health-tracker" topic:
1. Parse the message for food items and symptoms
2. Log to ~/clawd/memory/health-log.md with timestamp
3. Confirm what was logged

Set up 3 daily reminders:
- 8 AM: "🍳 Log your breakfast"
- 1 PM: "🥗 Log your lunch"
- 7 PM: "🍽️ Log your dinner and any symptoms"

Every Sunday, analyze the past week's log and identify patterns:
- Which foods correlate with symptoms?
- Are there time-of-day patterns?
- Any clear triggers?

Post the analysis to the health-tracker topic.
```

4. Optional: Add a memory file for OpenClaw to track known triggers and update it as patterns emerge.
