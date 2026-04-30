---
title: "Query-Focused Summarization"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [summarization, nlp, rag, query]
summary: "NLP task of generating summaries that specifically answer a user query, contrasted with generic summarization."
sources: ["from-local-to-global-graphrag"]
related: [graphrag, vector-rag, retrieval-augmented-generation]
---

# Query-Focused Summarization (QFS)

An NLP task where the goal is to generate a summary that specifically answers a given query, rather than providing a generic overview.

## Contrast with Generic Summarization

| Generic Summarization | Query-Focused Summarization |
|----------------------|------------------------------|
| Provides overall summary | Directly addresses specific question |
| Same output for any query | Output varies by query |
| "What is this document about?" | "How does X relate to Y?" |

## The Problem GraphRAG Solves

Traditional QFS methods don't scale to the large corpora indexed by typical RAG systems (millions of tokens). [[graphrag]] solves this by:

1. Building a [[knowledge-graph]] index
2. Using [[community-detection]] to partition into manageable units
3. Pregenerating [[community-summaries]]
4. Applying [[map-reduce-summarization]] for efficient querying

## Query Types

### Local Queries (Vector RAG handles well)
- "What is John's role at the company?"
- "When did Event X happen?"

### Global Queries (Requires QFS)
- "What are the main themes across all documents?"
- "How do different entities relate to each other?"
- "What trends emerge from this corpus?"

## Reference

- Central problem addressed in [[source/from-local-to-global-graphrag]]
- [[graphrag]] paper specifically targets global sensemaking queries