---
title: "Modular RAG"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, modular-rag, architecture, orchestration]
summary: "A meta-framework that decomposes RAG systems into three levels (Module, Sub-module, Operator) with routing, scheduling, and fusion mechanisms, enabling highly reconfigurable RAG architectures."
sources: ["modular-rag-transforming-rag-systems"]
related: [rag-paradigms, rag-flow-pattern, orchestration, knowledge-graph, graphrag]
---

# Modular RAG

A meta-framework for RAG systems that decomposes complex architectures into independent, reconfigurable components.

## Core Principle

Modular RAG treats RAG systems as **computational graphs** where:
- **Nodes** = Modules and Operators
- **Edges** = Control flow and data flow

## Three-Tier Architecture

| Level | Description | Example |
|-------|-------------|---------|
| **L1 Module** | Core RAG stages | Indexing, Retrieval, Generation, Orchestration |
| **L2 Sub-module** | Functional modules within L1 | Chunk Optimization, Query Rewrite |
| **L3 Operator** | Specific implementations | $f_{rewrite}$, $f_{rerank}$, HyDE |

## Key Innovation: Orchestration

Unlike linear RAG (Naive/Advanced), Modular RAG introduces three orchestration mechanisms:

1. **Routing**: Route queries to appropriate pipelines based on semantics/metadata
2. **Scheduling**: Control flow through rule/LLM/knowledge-guided decisions
3. **Fusion**: Aggregate results from multiple branches

## RAG Flow Patterns

Modular RAG identifies 4 main patterns:

- **[[rag-flow-pattern#linear]]**: Sequential modules in fixed order
- **[[rag-flow-pattern#conditional]]**: Route to different pipelines based on query
- **[[rag-flow-pattern#branching]]**: Parallel execution with branching
- **[[rag-flow-pattern#looping]]**: Iterative, recursive, or adaptive retrieval

## Relationship to Other RAG Paradigms

```
Naive RAG ⊂ Advanced RAG ⊂ Modular RAG
```

- **Naive RAG**: Basic retrieve-then-generate (1-hop)
- **Advanced RAG**: Pre/post retrieval processing (still linear)
- **Modular RAG**: Full orchestration with routing/scheduling/fusion

## Implementation Examples

| Method | Pattern Used |
|--------|--------------|
| [[graphrag]] | KG Index + Orchestration (Branching + Fusion) |
| RRR | Linear with Query Rewrite |
| FLARE | Loop (Adaptive Retrieval) |
| Self-RAG | Loop (Tuning-based) |
| ITER-RETGEN | Loop (Iterative) |
| ToC | Loop (Recursive) |

## Reference

- [[source/modular-rag-transforming-rag-systems]]
