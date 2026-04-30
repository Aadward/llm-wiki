---
title: "Vector RAG"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, retrieval, semantic-search, llm]
summary: "Conventional RAG approach using text embeddings and semantic similarity search to retrieve relevant records for queries."
sources: ["from-local-to-global-graphrag"]
related: [graphrag, retrieval-augmented-generation, knowledge-graph]
---

# Vector RAG

Conventional [[retrieval-augmented-generation]] approach using text embeddings and semantic similarity search.

## How It Works

1. **Embedding**: Convert text chunks into vector representations
2. **Retrieval**: Find chunks most similar to query in vector space
3. **Generation**: LLM generates answer using retrieved chunks

## Limitation

Vector RAG only supports **local queries** that can be answered with information from a small set of records. It fails on **global sensemaking questions** requiring understanding of the entire corpus.

Examples of questions Vector RAG cannot answer:
- "What are the main themes in the dataset?"
- "How do concepts in this corpus relate to each other?"
- "What are the key trends over time?"

## Comparison with GraphRAG

| Aspect | Vector RAG | [[graphrag]] |
|--------|-----------|--------------|
| Query Type | Local, fact-based | Global, sensemaking |
| Index | Embeddings | Knowledge Graph |
| Scalability | Good | Better (community summaries) |
| Comprehensiveness | Lower | Higher (72-83% win) |
| Diversity | Lower | Higher (62-82% win) |

## Reference

- Paper uses "SS" (semantic search) as baseline comparison in [[source/from-local-to-global-graphrag]]