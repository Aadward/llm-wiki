---
title: "Retrieval-Augmented Generation"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, llm, retrieval, generation]
summary: "RAG augments LLM generation with relevant information retrieved from external knowledge sources, enabling answers over large/private corpora."
sources: ["from-local-to-global-graphrag"]
related: [vector-rag, graphrag, knowledge-graph]
---

# Retrieval-Augmented Generation (RAG)

An approach that augments LLM generation with relevant information retrieved from external knowledge sources.

## Core Concept

```
Query → Retrieval → Retrieved Context → LLM Generation → Response
```

RAG enables LLMs to answer questions about information outside their training data or too large to fit in context window.

## Canonical RAG (Vector RAG)

1. Large corpus of text records
2. User query retrieves subset of semantically similar records
3. Retrieved records + query → LLM prompt
4. LLM generates answer

See [[vector-rag]] for details.

## Limitation

Vector RAG only works for **local queries** that can be answered from a small set of records. Fails for **global questions** requiring corpus-wide understanding.

## GraphRAG Extension

[[graphrag]] extends RAG to handle global sensemaking by:
- Building [[knowledge-graph]] index instead of pure embeddings
- Using [[community-detection]] for hierarchical partitioning
- Pregenerating community summaries for efficient querying

## Reference

- First described in Lewis et al., 2020 (cited in [[source/from-local-to-global-graphrag]])
- [[graphrag]] is the latest advancement in RAG technology