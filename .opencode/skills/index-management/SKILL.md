---
name: index-management
description: Maintain index.md and log.md as the wiki grows. Reference for index structure, update rules, and log parsing commands.
compatibility: opencode
---

# Index Management

## index.md Structure

```markdown
# Wiki Index

## Overview
- Total pages: N
- Last updated: YYYY-MM-DD

## Entities (N pages)
| Page | Summary | Updated |
|------|---------|---------|
| [[entity/name]] | One-line summary | YYYY-MM-DD |

## Concepts (N pages)
...

## Sources (N pages)
| Page | Summary | Source File | Created |
...

## Synthesis (N pages)
...
```

## Update Rules

### When to Update index.md

- New page created
- Page deleted
- Page renamed
- Summary changed
- Page type changed

### How to Update

1. Find appropriate section
2. Add/update entry in sorted order
3. Update count in section header
4. Update "Total pages" in Overview

## log.md Structure

```markdown
# Wiki Log

## [YYYY-MM-DD] type | Title
- Action 1
- Action 2
- Result: N pages created/updated
```

### Entry Types

- `ingest` - Source processed
- `query` - Question answered
- `lint` - Maintenance performed
- `edit` - Manual wiki edit
- `synthesis` - New analysis created

## Log Parsing

```bash
grep "^## \[" log.md | tail -10  # Last 10 entries
grep "^## .*ingest" log.md       # All ingests
grep "^## .*2026-04" log.md      # April entries
```

## Index Search Strategy

When answering queries:
1. Read `index.md` first
2. Identify relevant sections
3. Read candidate pages
4. Synthesize answer
5. Cite pages with `[[page]]` links