---
title: "Post-Retrieval Processing"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, post-retrieval, rerank, compression]
summary: "Post-retrieval techniques including reranking, compression, and selection to improve the quality of retrieved chunks before generation."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, retrieval, generation]
---

# Post-Retrieval Processing

Techniques for processing retrieved chunks before feeding to LLM.

## Challenges Addressed

1. **Lost in the Middle**: LLMs forget middle portion of long contexts
2. **Noise/Anti-fact**: Noisy or contradictory documents harm generation
3. **Context Window**: Too much relevant content to fit

## Three Approaches

### 1. Rerank

Reorder chunks to highlight most important ones.

**Methods**:
- **Rule-based**: Diversity, relevance, MRR metrics
- **Model-based**: LLM or trained reranker

### 2. Compression

Compress chunk content to reduce noise.

**Method**: LLMLingua
- Small language model (GPT-2, LLaMA-7B)
- Remove unimportant tokens
- Balance language integrity vs compression ratio

### 3. Selection

Remove irrelevant chunks entirely.

**Methods**:
- **Selective Context**: Remove low self-information content
- **LLM-Critique**: LLM evaluates relevance before generation

## Comparison

| Approach | Action | Result |
|----------|--------|--------|
| Rerank | Reorder | Order change, size same |
| Compression | Shrink | Shorter content |
| Selection | Remove | Fewer chunks |

## Reference

- [[source/modular-rag-transforming-rag-systems]] (Section IV-D)
