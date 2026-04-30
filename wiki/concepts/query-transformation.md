---
title: "Query Transformation"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, query, pre-retrieval, transformation]
summary: "Query transformation techniques (Rewrite, HyDE, Step-back) that convert the original query into a different form for improved retrieval."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, pre-retrieval, query-expansion]
---

# Query Transformation

Pre-retrieval technique to transform queries rather than expand them.

## Purpose

Original queries often fall short for retrieval:
- Poorly worded questions
- Language complexity and ambiguity
- Domain-specific terminology

## Methods

### Rewrite

Prompt LLM to rewrite query for retrieval.

**Real-world Impact**: Taobao's query rewrite improved recall for long-tail queries → increased GMV

### HyDE (Hypothetical Document Embeddings)

Generate hypothetical answer document, then retrieve based on answer-to-answer similarity.

**Key Insight**:
- Focus on **answer-to-answer** embedding similarity
- Not query-to-document similarity

**Variants**:
- Reverse HyDE: Generate hypothetical query for each chunk

### Step-back Prompting

Abstract query into high-level concept question.

**Process**:
1. Generate step-back question
2. Retrieve on both step-back and original
3. Combine results for generation

## Comparison with Query Expansion

| Aspect | Query Expansion | Query Transformation |
|--------|-----------------|---------------------|
| **Output** | Multiple queries | Single transformed query |
| **Method** | Diversity | Semantic shift |
| **Intent** | Coverage | Clarity/relevance |

## Reference

- [[source/modular-rag-transforming-rag-systems]] (Section IV-B2)
- [[concept/query-expansion]]
