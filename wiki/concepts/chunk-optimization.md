---
title: "Chunk Optimization"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, chunking, indexing, retrieval]
summary: "Strategies for optimizing document chunk size and overlap in RAG systems, balancing context capture against noise and computational cost."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, indexing]
---

# Chunk Optimization

Strategies for splitting documents into manageable chunks for RAG retrieval.

## Key Parameters

| Parameter | Symbol | Tradeoff |
|-----------|--------|----------|
| **Chunk Size** | Lᵢ = \|dᵢ\| | Larger = more context, more noise |
| **Overlap** | Lᵢᵒ = \|dᵢ ∩ dᵢ₊₁\| | Higher = better transitions, more redundancy |

## Strategies

### Sliding Window

Uses overlapping chunks with fixed window size.

**Limitation**:
- Imprecise context size control
- Potential truncation of words/sentences
- Lacks semantic considerations

### Metadata Attachment

Enrich chunks with metadata for filtered retrieval:

- Page number, file name, author, timestamp
- Summary, relevant questions

### Small-to-Big

Separate retrieval chunks from synthesis chunks:

- **Small chunks**: Better retrieval accuracy
- **Large chunks**: More context for generation

Variations:
1. Retrieve small summarized chunks → reference parent larger chunks
2. Retrieve sentences → reference surrounding text

## Trade-off Summary

| Chunk Size | Pros | Cons |
|-----------|------|------|
| **Large** | Captures more context | More noise, higher cost |
| **Small** | Less noise, faster | May miss context |

## Reference

- [[source/modular-rag-transforming-rag-systems]] (Section IV-A1)
