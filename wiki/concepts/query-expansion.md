---
title: "Query Expansion"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, query, pre-retrieval, expansion]
summary: "Query expansion techniques that enrich single queries into multiple queries to improve retrieval coverage, using Multi-Query or Sub-Query approaches."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, pre-retrieval, query-transformation]
---

# Query Expansion

Pre-retrieval technique to expand a single query into multiple queries.

## Purpose

Enrich query content to address:
- Lack of specific nuances
- Ambiguous terminology
- Complex question structure

## Methods

### Multi-Query

Expand via LLM prompt engineering into diverse queries.

**Formula**:
```
fqe(q) = {q₁, q₂, ..., qₙ}  ∀qᵢ ∉ Q
```

**Process**:
1. Generate multiple query variants
2. Execute in parallel
3. Aggregate results

**Concern**: May dilute original intent → instruct LLM to weight original query higher

### Sub-Query

Decompose complex problems using least-to-most prompting.

**Process**:
1. Decompose into sub-problems
2. Solve simplest first
3. Use intermediate results for complex sub-problems

**Variations**:
- Chain-of-Verification (CoVe): Validate expanded queries
- Sequential vs Parallel execution

## Reference

- [[source/modular-rag-transforming-rag-systems]] (Section IV-B1)
- Related: [[concept/query-transformation]]
