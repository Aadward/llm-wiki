---
title: "RAG Paradigms"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, paradigm, naive-rag, advanced-rag, modular-rag]
summary: "Evolution of RAG from Naive (basic retrieve-generate) to Advanced (with pre/post processing) to Modular (with orchestration), showing progressive complexity and capability."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, vector-rag, graphrag]
---

# RAG Paradigms

The evolution of RAG systems through three distinct paradigms.

## Paradigm Comparison

| Aspect | Naive RAG | Advanced RAG | Modular RAG |
|--------|-----------|--------------|------------|
| **Architecture** | Linear | Linear + Pre/Post | Orchestrated Graph |
| **Retrieval** | Single shot | Single shot | Multi-hop |
| **Query Processing** | Direct | Rewritten | Routed |
| **Post-Retrieval** | None | Rerank/Compress | Full pipeline |
| **Control Flow** | Fixed | Fixed | Dynamic |

## 1. Naive RAG

**Formula**:
```
q → R(q,D) → D^q → LLM([q,D^q]) → y
```

**Limitations**:
- Shallow semantic understanding of queries
- Retrieval redundancy and noise
- No intermediate processing

## 2. Advanced RAG

**Improvements over Naive**:
- Pre-retrieval: Query rewriting, expansion
- Post-retrieval: Reranking, compression, selection
- Hierarchical indexing

**Still**: Limited to linear, single-path retrieval generation

## 3. Modular RAG

**Key Innovation**: Orchestration layer with routing, scheduling, fusion

**Benefits**:
- Highly reconfigurable modules
- Supports branching, looping, conditional flows
- Easier debugging and optimization

## Inheritance

```
Naive RAG ⊂ Advanced RAG ⊂ Modular RAG
```

Every Naive RAG system is also an Advanced RAG system (with empty pre/post processing).
Every Advanced RAG system is also a Modular RAG system (with single linear flow).

## Reference

- [[source/modular-rag-transforming-rag-systems]]
- [[concept/modular-rag]]
