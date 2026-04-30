---
name: query
description: Answer a question by searching the wiki and synthesizing information. Use when user asks a knowledge question that the wiki might contain.
compatibility: opencode
---

# /query

## Purpose

Answer a question by searching the wiki and synthesizing information.

## Workflow

### Step 1: Read index.md

Scan `index.md` to identify relevant sections and candidate pages.

### Step 2: Read Relevant Pages

Read the candidate wiki pages identified from index.

### Step 3: Synthesize Answer

Combine information from multiple pages with citations:
- Use `[[page-name]]` to cite wiki pages
- Quote key passages with `>`blockquote
- Flag any contradictions noticed

### Step 4: Present Answer

Format answer appropriately:
- Markdown for general explanations
- Tables for comparisons
- Lists for enumerations

### Step 5: Offer to File

Ask user if answer should be filed back to wiki as a synthesis page:
- If yes, create `wiki/synthesis/[topic].md`
- Add frontmatter with `type: synthesis`
- Reference source pages
- Update `index.md` and `log.md`

## Note

Good candidates for filing:
- Comparisons between entities/concepts
- Analysis of a topic across multiple sources
- Synthesized explanation
- Timeline or chronology
- Answer to recurring question