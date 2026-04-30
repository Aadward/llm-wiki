---
title: "Modular RAG: Transforming RAG Systems into LEGO-like Reconfigurable Frameworks"
type: source
created: 2026-04-30
updated: 2026-04-30
source-file: "Modular RAG Transforming RAG Systems into LEGO-like Reconfigurable Frameworks.md"
tags: [rag, modular-rag, rag-paradigm, llm, knowledge-intensive, orchestration]
summary: "Modular RAG introduces a three-tier architecture (Module, Sub-module, Operator) that decomposes RAG systems into independent, reconfigurable components with routing, scheduling, and fusion mechanisms, transcending the linear retrieve-then-generate paradigm."
sources: []
related: []
---

# Modular RAG: Transforming RAG Systems into LEGO-like Reconfigurable Frameworks

## Key Takeaways

1. **Problem**: RAG systems have evolved beyond the simple "retrieve-then-generate" paradigm, making it difficult to unify disparate methods under a common framework.

2. **Solution**: Modular RAG decomposes RAG systems into three levels:
   - **L1 Module**: Core RAG stages (Indexing, Pre-retrieval, Retrieval, Post-retrieval, Generation, Orchestration)
   - **L2 Sub-module**: Functional modules within each module
   - **L3 Operator**: Specific functional implementations (e.g., $f_{rewrite}$, $f_{rerank}$, $f_{comp}$)

3. **Key Innovation**: Transcends linear architecture through:
   - **Routing**: Semantic/metadata-based route selection
   - **Scheduling**: Rule/LLM/knowledge-guided flow control
   - **Fusion**: Multi-branch result aggregation

4. **RAG Flow Patterns**: Identifies 4 main patterns plus tuning:
   - **Linear**: Sequential modules (Naive RAG → Advanced RAG → Modular RAG)
   - **Conditional**: Route to different pipelines based on query
   - **Branching**: Parallel execution (Pre-retrieval & Post-retrieval)
   - **Looping**: Iterative, Recursive, or Adaptive retrieval
   - **Tuning**: Retriever/Generator/Dual fine-tuning

5. **Relationship to Prior RAG**: Naive RAG ⊂ Advanced RAG ⊂ Modular RAG

## Pipeline Overview

```
Modular RAG Architecture (3-tier):

┌─────────────────────────────────────────────────────┐
│  L1 MODULES (Core Stages)                           │
│  ┌─────────┐ ┌────────────┐ ┌──────────┐           │
│  │ Indexing│ │Pre-retrieval│ │Retrieval │           │
│  └─────────┘ └────────────┘ └──────────┘           │
│  ┌─────────────┐ ┌───────────┐ ┌──────────────┐     │
│  │Post-retrieval│ │Generation │ │Orchestration│     │
│  └─────────────┘ └───────────┘ └──────────────┘     │
└─────────────────────────────────────────────────────┘
                        │
┌─────────────────────────────────────────────────────┐
│  L2 SUB-MODULES (Within each module)                │
│  e.g., Indexing: Chunk Optimization, Structure Org  │
└─────────────────────────────────────────────────────┘
                        │
┌─────────────────────────────────────────────────────┐
│  L3 OPERATORS (Specific implementations)           │
│  e.g., $f_{rewrite}$, $f_{rerank}$, $f_{comp}$     │
└─────────────────────────────────────────────────────┘
```

### Six Core Modules

1. **Indexing**: Chunk optimization (sliding window, metadata, small-to-big) + Structure organization (hierarchical, KG index)

2. **Pre-retrieval**: Query expansion (Multi-Query, Sub-Query), Query transformation (Rewrite, HyDE, Step-back), Query construction (Text-to-SQL/Cypher)

3. **Retrieval**: Retriever selection (Sparse/Dense/Hybrid), Retriever fine-tuning (SFT, LSR, Adapter)

4. **Post-retrieval**: Rerank, Compression (LLMLingua), Selection

5. **Generation**: Generator fine-tuning (Instruct-Tuning, RL), Verification

6. **Orchestration**: Routing (metadata/semantic/hybrid), Scheduling (rule/LLM/knowledge-guided), Fusion

## Entities Mentioned

- [[entity/yunfan-gao]] - Tongji University, lead author
- [[entity/yun-xiong]] - Fudan University, co-author
- [[entity/meng-wang]] - Tongji University, co-author
- [[entity/haofen-wang]] - Tongji University, corresponding author

### Key Methods (Operators)

- **RRR**: Query rewriting with reinforcement learning
- **HyDE**: Hypothetical Document Embeddings
- **FLARE**: Forward-Looking Active Retrieval
- **Self-RAG**: Self-reflective retrieval
- **REPLUG**: Retrieval-augmented black-box LMs
- **ITER-RETGEN**: Iterative retrieval-generation synergy
- **ToC**: Tree of Clarifications
- **RA-DIT**: Retrieval-augmented dual instruction tuning
- **DR-RAG**: Dynamic relevance RAG
- **PlanRAG**: Planning-based RAG
- **Multi-Head RAG**: Multi-head attention retriever

## Concepts Covered

- [[concept/modular-rag]] - The unifying meta-framework
- [[concept/rag-paradigms]] - Naive, Advanced, Modular RAG evolution
- [[concept/rag-flow-pattern]] - Linear, Conditional, Branching, Loop patterns
- [[concept/chunk-optimization]] - Chunk size and overlap strategies
- [[concept/query-expansion]] - Multi-Query and Sub-Query techniques
- [[concept/query-transformation]] - Rewrite, HyDE, Step-back methods
- [[concept/retriever-fine-tuning]] - SFT, LSR, adapter methods
- [[concept/post-retrieval-processing]] - Rerank, compression, selection
- [[concept/orchestration]] - Routing, scheduling, fusion
- [[concept/rag-flow-pattern#branching]] - Pre-retrieval and post-retrieval branching
- [[concept/rag-flow-pattern#looping]] - Iterative, recursive, adaptive retrieval

## Relationship with GraphRAG

Modular RAG provides the theoretical taxonomy that **includes** [[concept/graphrag]] as a concrete implementation:

| GraphRAG Component | Modular RAG Module/Operator |
|-------------------|----------------------------|
| LLM Entity/Relationship Extraction | Indexing > KG Index Operator |
| Knowledge Graph Construction | Indexing > Structure Organization |
| Leiden Community Detection | Orchestration > Knowledge-guided Scheduling |
| Pregenerated Community Summaries | Generation > Pre-computation |
| Map-Reduce Query | Orchestration > Branching + Fusion |

GraphRAG instantiates the [[concept/kg-index]] (Knowledge Graph Index) and Orchestration (Branching + Fusion) patterns described in Modular RAG.

## Notable Claims

> "Modular RAG transcends the traditional linear architecture, embracing a more advanced design that integrates routing, scheduling, and fusion mechanisms."

> "Advanced RAG is a special case of Modular RAG, while Naive RAG is a special case of Advanced RAG."

> "Modular RAG presents innovative opportunities for the conceptualization and deployment of RAG systems."

## RAG Paradigm Evolution

```
Naive RAG:    q → R(q,D) → D^q → LLM([q,D^q]) → y
                         ↓
Advanced RAG: q → Pre → R → Post → LLM → y
                         ↓
Modular RAG:  Orchestration-controlled flow with
              routing, scheduling, and fusion
```

## References

- arXiv: [2407.21059v1](https://arxiv.org/html/2407.21059v1)
- Affiliation: Tongji University, Fudan University
