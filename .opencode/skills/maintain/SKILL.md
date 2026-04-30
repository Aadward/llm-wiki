---
name: maintain
description: General wiki maintenance - fix broken links, update stale content, improve cross-references, ensure consistency. Use for routine maintenance tasks.
compatibility: opencode
---

# /maintain

## Purpose

General wiki maintenance — fix broken links, update stale content, improve cross-references.

## Operations

### Fix Broken Links

1. Search wiki for `[[broken-page]]` patterns
2. For each broken link:
   - If page exists with different name → update link
   - If page deleted → remove link or create placeholder
   - If should exist → create page

### Update Stale Pages

1. Find pages not updated since new sources added
2. Read newer relevant sources
3. Update page content and `updated` date
4. Mark superseded info with:
   ```markdown
   > [!superseded]
   > Previous claim about X updated per source-y.md (2026).
   ```

### Improve Cross-References

1. Find pages mentioning topics without wiki links
2. Add `[[concept/topic]]` links
3. Ensure each entity page references related entities

### Consistency Check

Verify:
- [ ] All entity names match exactly across pages
- [ ] All dates in `YYYY-MM-DD` format
- [ ] All tags lowercase hyphenated
- [ ] All page types valid: `entity`, `concept`, `source`, `synthesis`

## Update log.md

Append maintenance entry with actions taken.