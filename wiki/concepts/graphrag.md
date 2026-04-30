---
title: "GraphRAG"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, knowledge-graph, llm, retrieval-augmented-generation]
summary: "Graph-based RAG approach that uses LLM-extracted knowledge graphs and community detection to enable global sensemaking over large text corpora."
sources: ["from-local-to-global-graphrag"]
related: [vector-rag, knowledge-graph, query-focused-summarization, retrieval-augmented-generation, community-detection]
---

# GraphRAG

A graph-based approach to Retrieval-Augmented Generation (RAG) that enables global sensemaking over entire text corpora.

## Problem Solved

Conventional [[vector-rag]] fails on **global questions** like "What are the main themes in the dataset?" because it only retrieves locally relevant records. GraphRAG solves this through [[query-focused-summarization]] at the corpus level.

## How It Works

### Pipeline

1. **Document Chunking**: Split corpus into ~600 token chunks
2. **Entity Extraction**: LLM extracts [[knowledge-graph]] nodes (entities) and edges (relationships)
3. **Graph Construction**: Build graph from extracted entities and relationships
4. **Community Detection**: Use [[leiden-algorithm]] to partition graph hierarchically
5. **Community Summaries**: Pregenerate summaries for each community using LLM
6. **Query Answering**: Apply [[map-reduce-summarization]] over community summaries

### Query Flow

```
User Query → Map (community answers in parallel) → Reduce (global answer)
```

## Key Innovation

- Uses **LLM-extracted knowledge graphs** as index structure
- **Hierarchical community detection** enables multi-level summarization
- **Pregenerated community summaries** reduce query-time computation
- [[map-reduce-summarization]] enables scaling to million-token corpora

## Performance

GraphRAG significantly outperforms [[vector-rag]] on:
- **Comprehensiveness**: 72-83% win rate
- **Diversity**: 62-82% win rate

while using 9x-43x fewer tokens per query (C0 root-level communities).

## Implementation

- **Open Source**: [github.com/microsoft/graphrag](https://github.com/microsoft/graphrag)
- **Extensions**: Available for LangChain, LlamaIndex, NebulaGraph, Neo4J

## Related Concepts

- [[vector-rag]] - The conventional approach GraphRAG outperforms
- [[knowledge-graph]] - The graph structure GraphRAG builds
- [[community-detection]] - Algorithm used to partition the graph
- [[query-focused-summarization]] - The task GraphRAG enables
- [[retrieval-augmented-generation]] - The broader paradigm