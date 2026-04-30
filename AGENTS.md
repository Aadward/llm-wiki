# LLM Wiki - AGENTS.md

## Overview

This is a personal knowledge base built using the LLM Wiki pattern. The wiki sits between you and your raw sources — it's a structured, interlinked collection of markdown files that the LLM incrementally builds and maintains.

**Core principle**: The wiki is a persistent, compounding artifact. Cross-references are pre-built. Contradictions are flagged. Synthesis reflects everything you've read.

---

## Directory Structure

```
llm-wiki/
├── AGENTS.md          # This file - schema and conventions
├── index.md           # Content catalog (auto-updated by LLM)
├── log.md             # Chronological activity log (append-only)
├── SKILLS/            # Skill definitions for wiki operations
│   ├── wiki-maintenance.md
│   ├── source-ingest.md
│   └── index-management.md
├── raw/               # Immutable source documents (read-only)
│   ├── sources.md     # List of sources with metadata
│   └── assets/        # Downloaded images/attachments
└── wiki/              # LLM-generated content (LLM writes here)
    ├── entities/      # People, places, things
    ├── concepts/      # Topics, ideas, techniques
    ├── sources/       # Per-source summary pages
    ├── synthesis/     # Comparative analysis, thesis pages
    └── overview.md    # High-level wiki summary
```

---

## Wiki Page Conventions

### Frontmatter (Required)

Every wiki page MUST have YAML frontmatter:

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

# Page Title
```

### Wiki Links

Use Obsidian-compatible wiki links: `[[page-name]]` for internal links.

- Entity pages: `[[entity/person-name]]`
- Concept pages: `[[concept/topic-name]]`
- Source pages: `[[source/source-file-name]]`

### File Naming

- All lowercase, hyphenated
- Entity pages: `entity/person-name.md`
- Concept pages: `concept/topic-name.md`
- Source pages: `sources/source-title.md`

---

## Operations

### 1. Ingest (Process New Source)

**Trigger**: User drops a new file in `raw/` and requests processing.

**Workflow**:
1. Read the source file from `raw/`
2. Discuss key takeaways with user
3. Create source summary page in `wiki/sources/`
4. Update `index.md` with new entry
5. Update relevant entity pages (create if new)
6. Update relevant concept pages (create if new)
7. Append entry to `log.md`

**Single source may touch 10-15 wiki pages.**

### 2. Query (Answer Questions)

**Trigger**: User asks a question.

**Workflow**:
1. Read `index.md` to find relevant pages
2. Read relevant wiki pages
3. Synthesize answer with citations `[[page-name]]`
4. Present answer
5. **Optionally**: File good answers back into wiki as new pages

### 3. Lint (Health Check)

**Trigger**: User requests periodic maintenance.

**Check for**:
- Contradictions between pages
- Stale claims superseded by newer sources
- Orphan pages with no inbound links
- Important concepts lacking their own page
- Missing cross-references
- Data gaps fillable via web search

---

## Special Files

### index.md

Content-oriented catalog. Updated on every ingest.

```markdown
# Wiki Index

## Entities
| Page | Summary | Updated |
|------|---------|---------|
| [[entity/person-name]] | One-line summary | 2026-04-30 |

## Concepts
...

## Sources
...

## Synthesis
...
```

### log.md

Append-only chronological record.

```markdown
# Wiki Log

## [2026-04-30] ingest | Source Title
- Processed source.md
- Updated: entity pages, concept pages
- New pages created: 3

## [2026-04-30] query | Question summary
- Answered: ...
- Filed to: [[synthesis/analysis-name]]
```

Parse with: `grep "^## \[" log.md | tail -5`

---

## Obsidian Compatibility

- Wiki links: `[[double brackets]]`
- Frontmatter: YAML with `---` delimiters
- Internal images: Store in `raw/assets/`, reference as `![image](../raw/assets/image.png)`
- Tags: Use `#tag-name` in frontmatter and inline

### Recommended Obsidian Settings

1. **Files & Links**: Set attachment folder to `raw/assets/`
2. **Hotkeys**: Bind "Download attachments" to `Ctrl+Shift+D`
3. **Core Plugins**: Enable Daily Notes, Backlinks
4. **Community Plugins**:
   - **Dataview**: Query pages via frontmatter
   - **Marp**: Slide deck support
   - **Obsidian Git**: Version control

---

## Workflow Tips

- Ingest sources **one at a time** when possible
- Stay involved — read summaries, check updates, guide emphasis
- Good answers should be filed back into the wiki as new pages
- Use graph view to find orphan pages
- LLM handles maintenance; human provides direction

---

## Co-Evolution

This schema evolves with usage. Update AGENTS.md when:
- New page types needed
- Conventions change
- Workflows are refined
- New tools integrated