---
title: "From Local to Global: A GraphRAG Approach to Query-Focused Summarization"
type: source
created: 2026-04-30
updated: 2026-04-30
source-file: "From Local to Global A GraphRAG Approach to Query-Focused Summarization.md"
tags: [graphrag, rag, knowledge-graph, llm, summarization, query-focused]
summary: "GraphRAG uses LLM-extracted knowledge graphs and community detection to enable global sensemaking over large text corpora, outperforming conventional vector RAG on comprehensiveness and diversity for query-focused summarization tasks."
sources: []
related: ["modular-rag-transforming-rag-systems"]
---

# From Local to Global: A GraphRAG Approach to Query-Focused Summarization

## Key Takeaways

1. **Problem**: Conventional vector RAG fails on global questions requiring understanding of an entire corpus (e.g., "What are the main themes?"). This is inherently a Query-Focused Summarization (QFS) task.

2. **Solution**: GraphRAG builds a graph index from source documents using LLM entity/relationship extraction, then partitions into communities with Leiden algorithm, pregenerates community summaries, and uses map-reduce for query answering.

3. **Results**: GraphRAG substantially outperforms vector RAG on comprehensiveness (72-83% win rate) and diversity (62-82% win rate) for global sensemaking questions on 1M token datasets.

4. **Efficiency**: Root-level community summaries (C0) require 9x-43x fewer tokens per query compared to text summarization while maintaining quality.

5. **Open Source**: Implemented in [microsoft/graphrag](https://github.com/microsoft/graphrag), with extensions for LangChain, LlamaIndex, NebulaGraph, and Neo4J.

## Pipeline Overview

```
Source Documents → Text Chunks → Entities & Relationships → Knowledge Graph
                                                                      ↓
                                                        Graph Communities (Leiden)
                                                                      ↓
                                                        Community Summaries
                                                                      ↓
                                              Community Answers → Global Answer
```

### Key Stages

1. **Text Chunking**: Documents split into ~600 token chunks with 100 token overlap
2. **Entity Extraction**: LLM extracts entities, relationships, and claims from chunks
3. **Graph Construction**: Nodes (entities) and edges (relationships) form knowledge graph
4. **Community Detection**: Leiden algorithm partitions graph hierarchically
5. **Community Summaries**: LLM generates report-like summaries at each community level
6. **Query Answering**: Map-reduce over community summaries for global answers

## Entities Mentioned

- [[entity/microsoft-research]] - Primary research affiliation
- [[entity/darren-edge]] - Microsoft Research, co-lead author
- [[entity/ha-trinh]] - Microsoft Research, co-lead author
- [[entity/kevin-scott]] - Microsoft CTO, podcast host (for Podcast dataset)
- [[entity/gpt-4]] - LLM used for evaluation
- [[entity/leiden-algorithm]] - Community detection algorithm used
- [[entity/claimify]] - LLM-based claim extraction tool

## Concepts Covered

- [[concept/graphrag]] - Graph-based RAG approach for global sensemaking
- [[concept/vector-rag]] - Conventional semantic search RAG approach
- [[concept/query-focused-summarization]] - Task of generating summaries answering specific queries
- [[concept/knowledge-graph]] - Graph representation of entities and relationships
- [[concept/retrieval-augmented-generation]] - RAG paradigm
- [[concept/community-detection]] - Leiden algorithm for graph partitioning
- [[concept/map-reduce-summarization]] - Parallel then aggregate approach
- [[concept/llm-as-a-judge]] - LLM evaluation methodology
- [[concept/adaptive-benchmarking]] - Dynamic benchmark generation

## Notable Claims

> "GraphRAG leads to substantial improvements over a conventional RAG baseline for both the comprehensiveness and diversity of generated answers."

> "The use of retrieval-augmented generation (RAG) to retrieve relevant information... fails on global questions directed at an entire text corpus, such as 'What are the main themes in the dataset?'"

> "Sensemaking tasks require reasoning over connections... in order to anticipate their trajectories and act effectively."

## Evaluation Results

| Metric | GraphRAG vs Vector RAG Win Rate |
|--------|-------------------------------|
| Comprehensiveness | 72-83% (p<.001) |
| Diversity | 62-82% (p<.001) |
| Directness | Vector RAG wins (control) |

## Implementation Details

- **Context window**: 8k tokens for all conditions
- **Graph indexing**: 281 minutes for 1M token Podcast dataset
- **Leiden detection**: Using graspologic library
- **Claim extraction**: Claimify for factual claim verification

## References

- arXiv: [2404.16130v2](https://arxiv.org/html/2404.16130v2)
- Code: [github.com/microsoft/graphrag](https://github.com/microsoft/graphrag)
- Extensions: LangChain, LlamaIndex, NebulaGraph, Neo4J