---
name: wiki-conventions
description: Wiki page conventions and consistency rules. Reference for frontmatter format, wiki links, file naming, and quality standards.
compatibility: opencode
---

# Wiki Conventions

## Page Types

- `entity` - People, places, things
- `concept` - Topics, ideas, techniques
- `source` - Source document summary pages
- `synthesis` - Comparative analysis, thesis pages

## Frontmatter (Required)

```yaml
---
title: "Page Title"
type: entity|concept|source|synthesis
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [tag1, tag2]
sources: []  # Related source files
related: []  # Linked wiki pages
---
```

## Wiki Links

Use Obsidian-compatible wiki links: `[[page-name]]`

- Entity pages: `[[entity/person-name]]`
- Concept pages: `[[concept/topic-name]]`
- Source pages: `[[source/source-file-name]]`

## File Naming

- All lowercase, hyphenated
- Entity: `entity/person-name.md`
- Concept: `concept/topic-name.md`
- Source: `sources/source-title.md`

## Callout Types

```markdown
>[!contradiction]
> Opposing claims detected. Needs review.

>[!stale]
> This claim may be superseded by newer sources.

>[!superseded]
> Previous claim updated per newer source.
```

## Consistency Rules

- Entity names: Match exactly across all pages
- Dates: Always `YYYY-MM-DD` format
- Tags: Lowercase, hyphenated
- Types: Only `entity`, `concept`, `source`, `synthesis`