---
name: ingest
description: Ingest a new source document into the wiki. Process raw sources, create summary pages, update entities/concepts, maintain index and log. Use when user drops a new file in raw/ and wants to process it.
compatibility: opencode
---

# /ingest

## Purpose

Ingest a new source document into the wiki. This is the primary workflow for adding knowledge.

## Pre-Check

1. Confirm source file exists in `raw/`
2. Confirm source metadata added to `raw/sources.md`
3. Inform user ingest is starting

## Workflow

### Step 1: Read Source

Read the complete source file from `raw/`.

### Step 2: Analyze & Discuss

Discuss with user:
- Key takeaways
- Important entities to extract
- Concepts to highlight
- What to emphasize

### Step 3: Create Source Summary Page

Create `wiki/sources/[source-name].md` following the source page template with:
- YAML frontmatter (type: source, created, source-file, tags, summary)
- Key Takeaways section
- Entities Mentioned with wiki links
- Concepts Covered with wiki links
- Notable Claims as blockquotes

### Step 4: Update Entities

For each entity mentioned:
- **Exists**: Read → Add info → Update `updated` date → Add to sources list
- **New**: Create `wiki/entities/[name].md` → Add frontmatter → Write description → Link to source

### Step 5: Update Concepts

For each concept covered:
- **Exists**: Read → Integrate info → Note source
- **New**: Create `wiki/concepts/[name].md` → Add frontmatter → Write definition → Link to source

### Step 6: Update Synthesis

If source relates to existing synthesis pages:
- Integrate relevant findings
- Update comparison tables
- Add note about new source

### Step 7: Update index.md

Add entry in correct category section with link, summary, and date.

### Step 8: Update log.md

Append ingest entry with:
- Date and source title
- Processed file path
- Created/updated pages list
- User discussion notes

## Completion

Show user:
- Summary of created/updated pages
- Links to new pages
- Suggested follow-up questions