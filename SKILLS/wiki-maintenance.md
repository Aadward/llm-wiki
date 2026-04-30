# Wiki Maintenance Skill

## Purpose

Maintain wiki health as it grows. The LLM does all bookkeeping — updating cross-references, noting contradictions, keeping summaries current.

---

## Maintenance Operations

### 1. Cross-Reference Check

When creating/updating a page, ensure:
- All mentioned entities have links to their pages
- All concepts link to concept pages
- All sources cite source pages with `[[source/...]]`

### 2. Contradiction Detection

When ingesting new sources, check existing pages for:
- Opposing claims
- Outdated information
- Superceded data

Flag in page: add `[!contradiction]` callout:
```markdown
>[!contradiction]
> Source claims X, but [[entity/old-page]] says Y. Needs review.
```

### 3. Orphan Page Detection

Find pages with no inbound links:
- Check `index.md` for pages only in "outbound" sections
- Use Obsidian graph view to identify orphans
- Create linking opportunities or merge/delete

### 4. Stale Content Check

For each page:
- Compare `updated` date with newer sources
- Mark claims that need revision with `[!stale]` callout
- Schedule update during next relevant ingest

---

## Page Update Workflow

### When updating an existing page:

1. Read current page content
2. Read new relevant sources
3. Merge new information, preserve valid existing content
4. Update `updated` date in frontmatter
5. Add update note in page footer:
   ```markdown
   ---
   updated: 2026-04-30
   last-source: source-file.md
   ---

   # Updated Page

   Content...

   <!-- Last updated by LLM on 2026-04-30 -->
   ```

### Backward Compatibility

Never delete old information — mark it as superseded:
```markdown
> [!superseded]
> Previous claim about X was based on sources from 2025. Updated per source-y.md (2026).
```

---

## Link Maintenance

### Broken Link Fix

1. Search for `[[broken-page]]` patterns in wiki
2. Find target page or create placeholder
3. If page deleted, update all references

### Missing Links

When reading a page about topic X that lacks a concept page:
1. Create `wiki/concepts/x.md` with brief definition
2. Add link from source page
3. Update `index.md`

---

## Consistency Rules

- **Entity names**: Match exactly across all pages (case, hyphenation)
- **Dates**: Always `YYYY-MM-DD` format
- **Tags**: Lowercase, hyphenated
- **Types**: `entity`, `concept`, `source`, `synthesis` only

---

## Quality Checklist

After any maintenance operation:
- [ ] All wiki links resolve
- [ ] Frontmatter complete and valid
- [ ] `index.md` updated if page list changed
- [ ] `log.md` entry added
- [ ] No broken references to deleted content