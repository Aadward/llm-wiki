# Source Ingest Skill

## Purpose

Process new sources into the wiki. When user drops a file in `raw/` and requests processing, follow this workflow.

---

## Pre-Ingest Checklist

- [ ] Source file exists in `raw/`
- [ ] Source added to `raw/sources.md` with metadata
- [ ] User informed that ingest is starting

---

## Ingest Workflow

### Step 1: Read Source

Read the complete source file. If multi-file (e.g., zip of articles):
- Extract and list all files
- Process main document first
- Note supplementary materials

### Step 2: Analyze & Discuss with User

Before writing, discuss with user:
- Key takeaways
- Important entities to extract
- Concepts to highlight
- What to emphasize

### Step 3: Create Source Summary Page

Create `wiki/sources/[source-name].md`:

```yaml
---
title: "Source Title"
type: source
created: 2026-04-30
source-file: raw/source-file.md
tags: [tag1, tag2]
summary: "One-paragraph summary of the source"
---

# Source Title

## Key Takeaways

- Point 1
- Point 2

## Entities Mentioned
- [[entity/person]] - relation
- [[entity/org]] - role

## Concepts Covered
- [[concept/topic]]
- [[concept/topic]]

## Notable Claims

> Quote or key fact from source

## Personal Notes

_Your notes from discussion_
```

### Step 4: Update Entities

For each entity mentioned:

**If entity page exists:**
- Read existing page
- Add new information
- Update `updated` date
- Add to `sources` list in frontmatter

**If new entity:**
- Create `wiki/entities/[entity-name].md`
- Add frontmatter with `type: entity`
- Write brief description
- Link back to source

### Step 5: Update Concepts

For each concept covered:

**If concept page exists:**
- Read existing page
- Integrate new information
- Note source in relevant sections

**If new concept:**
- Create `wiki/concepts/[concept-name].md`
- Add frontmatter with `type: concept`
- Write definition and context
- Link to source

### Step 6: Update Synthesis

If source relates to existing synthesis/analysis:
- Integrate relevant findings
- Update comparison tables
- Note in synthesis page

### Step 7: Update index.md

Add entry to appropriate section:

```markdown
| [[source/source-title]] | One-line summary | 2026-04-30 |
```

### Step 8: Update log.md

Append to log:

```markdown
## [2026-04-30] ingest | Source Title

- Processed: raw/source-file.md
- Created pages: entity/person, concept/topic, [[sources/source-title]]
- Updated pages: entity/existing
- Notes: [user discussion points]
```

---

## Image Handling

If source contains images:

1. User downloads images via Obsidian hotkey to `raw/assets/`
2. LLM references images in text:
   ```markdown
   ![diagram](../raw/assets/image.png)
   ```
3. Note: LLM reads text first, then views images separately for additional context

---

## Batch Ingest Mode

If user requests multiple sources at once:

1. Process each source sequentially
2. After all processed, run lint pass
3. Update cross-references between batch sources
4. Single log entry with all sources listed

---

## Completion

After ingest complete:
- Show user summary of changes
- Point to new pages
- Suggest follow-up questions