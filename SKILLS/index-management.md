# Index Management Skill

## Purpose

Maintain `index.md` and `log.md` as the wiki grows. These files are the primary navigation aids.

---

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
| Page | Summary | Updated |
|------|---------|---------|
| [[concept/name]] | One-line summary | YYYY-MM-DD |

## Sources (N pages)
| Page | Summary | Source File | Created |
|------|---------|-------------|---------|
| [[sources/name]] | Summary | raw/file.md | YYYY-MM-DD |

## Synthesis (N pages)
| Page | Summary | Updated |
|------|---------|---------|
| [[synthesis/name]] | One-line summary | YYYY-MM-DD |
```

---

## Update Rules

### When to Update index.md

- New page created
- Page deleted
- Page renamed
- Summary changed
- Page type changed

### How to Update

1. Find appropriate section
2. Add/update entry in sorted order (alphabetical within section)
3. Update count in section header
4. Update "Total pages" in Overview

---

## log.md Structure

```markdown
# Wiki Log

Chronological record of wiki activity.

## [YYYY-MM-DD] type | Title
- Action 1
- Action 2
- Result: N pages created/updated

## [YYYY-MM-DD] type | Title
...
```

### Entry Types

- `ingest` - Source processed
- `query` - Question answered
- `lint` - Maintenance performed
- `edit` - Manual wiki edit
- `synthesis` - New analysis created

---

## Log Parsing

User can extract recent activity:
```bash
grep "^## \[" log.md | tail -10  # Last 10 entries
grep "^## .*ingest" log.md       # All ingests
grep "^## .*2026-04" log.md      # April entries
```

---

## Index Search Strategy

When answering queries:

1. Read `index.md` first
2. Identify relevant sections
3. Read candidate pages
4. Synthesize answer
5. Cite pages with `[[page]]` links

This approach works well up to ~100 sources, hundreds of pages.

---

## Maintenance

### Quarterly Review

- Verify all index entries link to existing pages
- Check for duplicate entries
- Clean up orphaned entries (page deleted but index entry remains)
- Update counts

### Renaming Pages

If a page is renamed:
1. Update all links from other wiki pages
2. Update `index.md` entry
3. Add redirect note in old location (optional)