---
title: "KG Index"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, knowledge-graph, indexing, kg-index]
summary: "Knowledge Graph-based indexing approach where documents are structured as graphs with entities as nodes and relationships as edges, enabling semantic retrieval and contextually coherent responses."
sources: ["modular-rag-transforming-rag-systems", "from-local-to-global-graphrag"]
related: [knowledge-graph, graphrag, modular-rag, indexing]
---

# KG Index

A document indexing approach using Knowledge Graphs instead of pure vector embeddings.

## Concept

Transform documents into graph structures:
- **Nodes** (V): Document structures (passages, pages, tables)
- **Edges** (E): Semantic/lexical similarity, belonging relations
- **Node Features** (X): Text or markdown content

## Benefits

1. **Consistency**: Clarifies connections between concepts/entities
2. **Reduced Mismatch**: Explicit relationships reduce retrieval errors
3. **Improved Coherence**: Enables contextually coherent responses
4. **Structured Retrieval**: Transforms retrieval into query instructions

## GraphRAG Implementation

[[graphrag]] is a concrete implementation of KG Index:

1. **Entity Extraction**: LLM extracts entities from text chunks
2. **Relationship Detection**: LLM identifies connections between entities
3. **Graph Construction**: Build graph G = {V, E, X}
4. **Community Detection**: Leiden algorithm partitions the graph
5. **Query Execution**: Map-reduce over community summaries

## Comparison with Vector Index

| Aspect | Vector Index | KG Index |
|--------|-------------|----------|
| **Representation** | Embeddings | Graph structure |
| **Relationships** | Implicit (cosine sim) | Explicit edges |
| **Query Type** | Local (semantic similar) | Local + Global |
| **Interpretability** | Low | High |

## Reference

- [[source/modular-rag-transforming-rag-systems]] (Section IV-A2)
- [[source/from-local-to-global-graphrag]]
- [[concept/knowledge-graph]]
