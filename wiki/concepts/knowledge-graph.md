---
title: "Knowledge Graph"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [graph, knowledge, llm, extraction]
summary: "Graph representation where nodes are entities and edges are relationships between them, enabling structured knowledge representation."
sources: ["from-local-to-global-graphrag"]
related: [graphrag, entity-extraction, relationship-extraction]
---

# Knowledge Graph

A graph-based representation of knowledge where **nodes** represent entities and **edges** represent relationships between them.

## In GraphRAG

[[graphrag]] builds knowledge graphs from text corpora using LLM-based extraction:

### Node Types
- **Entities**: People, places, organizations, events
- Each node has a **description** (LLM-generated summary)

### Edge Types
- **Relationships**: Connections between entities
- **Edge weight**: Number of times relationship detected across corpus
- Each edge has a **description**

### Additional Elements
- **Claims**: Factual statements about entities (dates, events, interactions)

## Construction Process

1. LLM extracts entities, relationships, and claims from text chunks
2. Multiple extractions of same element are reconciled (entity matching)
3. Descriptions are aggregated and summarized
4. Result: Graph index ready for community detection

## Entity Extraction Example

From text: "NeoChip's shares surged... acquired by Quantum Systems in 2016..."

Extracted:
- **Node**: NeoChip - "publicly traded company specializing in low-power processors"
- **Node**: Quantum Systems - "firm that previously owned NeoChip"
- **Edge**: Quantum Systems → NeoChip - "owned from 2016 until IPO"
- **Claim**: "Quantum Systems acquired NeoChip in 2016"

## Related Concepts

- [[entity-extraction]] - The process of extracting nodes
- [[relationship-extraction]] - The process of extracting edges
- [[graphrag]] - System that builds and uses knowledge graphs
- [[community-detection]] - Applied to knowledge graphs to create partitions