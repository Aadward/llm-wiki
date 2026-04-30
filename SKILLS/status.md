# /status

## Purpose

Show current wiki status — page counts, recent activity, health metrics.

## Output

### Overview Stats
- Total pages by type
- Total sources ingested
- Last activity date

### Recent Activity

Show last 5 entries from `log.md`:
```bash
grep "^## \[" log.md | tail -5
```

### Wiki Health
- Pages with broken links: N
- Orphan pages: N
- Pages needing update: N

### Quick Links
- `[[index.md]]` - Full index
- `[[log.md]]` - Activity log
- `[[raw/sources.md]]` - Source list