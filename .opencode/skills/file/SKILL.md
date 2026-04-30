---
name: file
description: File an answer or analysis back into the wiki as a synthesis page. Use after /query when the answer has lasting value and should be preserved.
compatibility: opencode
---

# /file

## Purpose

File an answer or analysis back into the wiki as a synthesis page.

## When to Use

After answering a query, if the answer has lasting value:
- Comparison tables
- Analysis of topic across sources
- Synthesized explanation
- Timeline or chronology
- Answer to recurring question

## Workflow

### Step 1: Create Page

Create `wiki/synthesis/[topic-name].md`:

```yaml
---
title: "Topic Name"
type: synthesis
created: 2026-04-30
updated: 2026-04-30
tags: [tag1, tag2]
related: [[entity/x]], [[concept/y]], [[sources/z]]
---

# Topic Name

## Summary

One-paragraph overview.

## Details

Content...

## Sources

- [[source/page]] - evidence
- [[source/page]] - evidence
```

### Step 2: Update index.md

Add entry to Synthesis section with summary.

### Step 3: Update log.md

Append entry:
```markdown
## [2026-04-30] synthesis | Topic Name

- Filed answer to query from [date]
- Created [[synthesis/topic-name]]
```

## Linking

After filing:
- Ensure all internal references use `[[wiki-links]]`
- Backlink from related entity/concept pages