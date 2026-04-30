# /lint

## Purpose

Perform a health check on the wiki to maintain quality as it grows.

## Checks to Perform

### 1. Contradiction Detection

Search for opposing claims across pages:
- Compare claims about same entities/concepts from different sources
- Mark contradictions with callout:
  ```markdown
  >[!contradiction]
  > Source A claims X, but [[page]] says Y. Needs review.
  ```

### 2. Stale Content Detection

For each page with `updated` date older than recent sources:
- Mark claims needing revision:
  ```markdown
  >[!stale]
  > This claim may be superseded by newer sources.
  ```

### 3. Orphan Page Detection

Find pages with no inbound links:
- Check which pages are cited but don't cite others
- Use Obsidian graph view to identify orphans visually
- Create linking opportunities or merge/delete

### 4. Missing Concept Pages

Identify important topics mentioned but lacking dedicated pages:
- Create placeholder `wiki/concepts/[topic].md`
- Add brief definition
- Update references

### 5. Broken Link Check

Search for `[[broken-reference]]` patterns:
- Either create target page
- Or update link to valid target

### 6. Data Gap Analysis

Identify:
- Areas with sparse coverage
- Questions that need more sources
- Web searches that could fill gaps

## Output

Report findings to user:
- Issues found (grouped by severity)
- Suggested fixes
- Pages needing update

## Update log.md

Append lint entry with findings summary.