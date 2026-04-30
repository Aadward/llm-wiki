---
title: "Community Detection"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [graph, algorithm, community-detection, partition]
summary: "Algorithm to partition graph nodes into mutually exclusive, collectively exhaustive communities of strongly connected nodes."
sources: ["from-local-to-global-graphrag"]
related: [graphrag, knowledge-graph, leiden-algorithm]
---

# Community Detection

The process of partitioning graph nodes into groups (communities) where nodes within a community are more densely connected to each other than to nodes in other communities.

## In GraphRAG

[[graphrag]] uses community detection to create a **hierarchical partitioning** of the [[knowledge-graph]]:

1. Leiden algorithm detects communities at root level
2. Recursively detects sub-communities within each community
3. Continues until leaf communities cannot be further partitioned
4. Each level provides different granularity for summarization

## Why Hierarchical?

Different levels offer trade-offs:
- **Root level (C0)**: Few communities, 9x-43x fewer tokens, good for broad questions
- **Leaf level (C3)**: Many communities, more detail, higher token cost

## Algorithm Used

[[leiden-algorithm]] (Traag et al., 2019) - Selected for:
- Scalability to large graphs
- Hierarchical nature
- Quality guarantees

## GraphRAG Statistics

| Dataset | Nodes | Edges |
|---------|-------|-------|
| Podcast | 8,564 | 20,691 |
| News | 15,754 | 19,520 |

## Related Concepts

- [[leiden-algorithm]] - Specific algorithm used
- [[graphrag]] - System that employs community detection
- [[knowledge-graph]] - Input graph structure
- [[community-summaries]] - Output generated from communities