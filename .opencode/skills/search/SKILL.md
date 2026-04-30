---
name: search
description: Search the wiki for pages matching a query using the index file. Use when user wants to find specific topics or information in the wiki.
compatibility: opencode
---

# /search

## Purpose

Search the wiki for pages matching a query using the index file.

## Workflow

### Step 1: Read index.md

Show index structure and all page titles.

### Step 2: Identify Candidates

Match query keywords against:
- Page titles
- Summaries in index
- Tags in frontmatter

### Step 3: Read Candidate Pages

Read the most relevant pages (max 5).

### Step 4: Report

Return:
- Matching page links with summaries
- Relevant passages
- Next steps (query, file, etc.)