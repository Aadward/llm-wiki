---
title: "RAG Flow Patterns"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, flow-pattern, orchestration, linear, branching, looping]
summary: "Four RAG flow patterns (Linear, Conditional, Branching, Looping) plus Tuning pattern, each defining how modules are orchestrated for different RAG scenarios."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, orchestration, rag-paradigms]
---

# RAG Flow Patterns

The common structural patterns for orchestrating RAG modules.

## Pattern Overview

| Pattern | Description | Use Case |
|---------|-------------|----------|
| **Linear** | Sequential modules | Simple Q&A |
| **Conditional** | Route-based selection | Multi-scenario queries |
| **Branching** | Parallel execution | Diverse result generation |
| **Looping** | Iterative refinement | Complex multi-hop reasoning |

---

## Linear Pattern

Sequential execution of modules in fixed order.

```
M₁ → M₂ → M₃ → ... → Mₙ
```

**Examples**:
- RRR (Query Rewrite → Retrieve → Generate)
- Standard Advanced RAG

---

## Conditional Pattern

Route to different pipelines based on query characteristics.

```
         q
          ↓
    ┌─────────┐
    │ Router  │ fᵣ(q)
    └────┬────┘
    ┌────┴────┐
    ▼         ▼
   Mⱼ        Mₖ
```

**Examples**:
- Different retrieval sources for different query types
- Specialized flows for different domains

---

## Branching Pattern

Split into parallel branches, then merge results.

### Pre-Retrieval Branching (Multi-Query)

```
q → Expand → Parallel Retrieval → Parallel Generate → Merge
```

### Post-Retrieval Branching (Single Query)

```
q → Retrieve → Parallel Generate → Merge
```

**Examples**:
- REPLUG: Parallel generation per retrieved chunk

---

## Looping Pattern

Iterative retrieval and generation with feedback.

### Iterative

Fixed number of iterations with judge.

```
q → loop(Retrieve → Generate → Judge) → Synthesize
```

### Recursive

Tree-like deepening with termination condition.

```
q → while depth < Kₘₐₓ:
       Retrieve → Generate → Derive new queries
```

### Adaptive (Active)

LLM decides when to retrieve.

```
FLARE: Generate → Check confidence → Retrieve if needed
Self-RAG: Generate special tokens to trigger retrieval
```

---

## Tuning Pattern

Fine-tuning components for optimization.

| Type | Focus | Method |
|------|-------|--------|
| Retriever FT | Embedding optimization | SFT, LSR, Adapter |
| Generator FT | Response quality | Instruct-Tuning, Distillation |
| Dual FT | End-to-end alignment | RA-DIT |

---

## Reference

- [[source/modular-rag-transforming-rag-systems]]
- [[concept/modular-rag]]
