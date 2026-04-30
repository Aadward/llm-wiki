---
title: "Orchestration"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, orchestration, routing, scheduling, fusion]
summary: "The control layer in Modular RAG that governs routing (module selection), scheduling (flow control), and fusion (result aggregation) across the RAG pipeline."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, rag-flow-pattern]
---

# Orchestration

The control module that governs RAG process flow in Modular RAG architecture.

## Three Core Functions

### 1. Routing

Selects which modules/flows to use based on query analysis.

**Types**:
- **Metadata Routing**: Keyword matching against predefined rules
- **Semantic Routing**: Intent detection via LLM
- **Hybrid Routing**: Combines both with weighted scoring

### 2. Scheduling

Controls execution order and iteration.

**Types**:
- **Rule Judge**: Score-based threshold decisions
- **LLM Judge**: Prompt-engineered decisions
- **Knowledge-guided**: KG-based reasoning paths

### 3. Fusion

Aggregates results from multiple branches.

**Methods**:
- **LLM Fusion**: Analyze and integrate via LLM
- **Weighted Ensemble**: Token probability weighting
- **RRF (Reciprocal Rank Fusion)**: Rank-based aggregation

## In RAG Flow Patterns

| Pattern | Orchestration Role |
|---------|-------------------|
| Linear | Minimal (fixed pipeline) |
| Conditional | Routing only |
| Branching | Fusion only |
| Looping | Scheduling + Routing |

## Implementation Examples

| Method | Orchestration Type |
|--------|-------------------|
| FLARE | LLM Judge (scheduling) |
| Self-RAG | Token-based triggers (scheduling) |
| [[graphrag]] | Knowledge-guided + Fusion |

## Reference

- [[source/modular-rag-transforming-rag-systems]]
- [[concept/modular-rag]]
- [[concept/rag-flow-pattern]]
