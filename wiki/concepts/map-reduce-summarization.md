---
title: "Map-Reduce Summarization"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [summarization, map-reduce, parallel, llm]
summary: "Two-stage approach: Map generates parallel partial answers, Reduce combines them into final answer."
sources: ["from-local-to-global-graphrag"]
related: [graphrag, query-focused-summarization]
---

# Map-Reduce Summarization

A two-stage approach for processing and aggregating information from multiple sources.

## Stages

### Map Phase

1. Input (community summaries or text chunks) is shuffled and divided into chunks
2. LLM generates **partial answers** for each chunk **independently and in parallel**
3. Each partial answer receives a **helpfulness score** (0-100)
4. Answers with score 0 are filtered out

### Reduce Phase

1. Partial answers sorted by helpfulness score (descending)
2. Iteratively added to new context window until token limit reached
3. Final context used to generate **global answer**

## Why Shuffle?

Ensures relevant information is distributed across chunks, not concentrated in single context window.

## In GraphRAG

Used in two places:

1. **Community Summary Generation**: Leaf-level summaries use map-reduce over element summaries
2. **Query Answering**: Community answers → Global answer via map-reduce

## Advantage

Enables processing of information that exceeds context window limits by:
- Parallel processing (map)
- Hierarchical aggregation (reduce)

## Related Concepts

- [[graphrag]] - System that uses this approach
- [[query-focused-summarization]] - The task it enables
- [[community-summaries]] - What gets processed