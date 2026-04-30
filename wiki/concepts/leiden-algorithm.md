---
title: "Leiden Algorithm"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [algorithm, graph, community-detection]
summary: "A community detection algorithm that improves on Louvain, providing better quality partitions and hierarchical structure."
sources: ["from-local-to-global-graphrag"]
related: [community-detection, graphrag]
---

# Leiden Algorithm

A community detection algorithm introduced by Traag et al. in 2019, used in [[graphrag]] for hierarchical graph partitioning.

## Why Leiden?

Selected over other community detection algorithms (e.g., Louvain) because:
- Provides **hierarchical** community structure
- Better quality partitions
- More scalable

## How It Works

1. Initial partition using Louvain-style method
2. Refinement step to improve partition quality
3. Recursively applied within each community
4. Result: Tree of communities from root to leaves

## GraphRAG Usage

[[graphrag]] uses Leiden via the **graspologic** library:
- Input: [[knowledge-graph]] built from LLM extraction
- Output: Hierarchical community structure
- Each level becomes a different granularity for [[community-summaries]]

## Reference

- Traag et al., 2019 (cited in [[source/from-local-to-global-graphrag]])
- Implementation: graspologic library (Chung et al., 2019)

## Related Concepts

- [[community-detection]] - The broader problem Leiden solves
- [[graphrag]] - System that uses Leiden
- [[community-summaries]] - Generated from Leiden-detected communities