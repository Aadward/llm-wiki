---
title: "Adaptive Benchmarking"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [evaluation, benchmarking, llm]
summary: "Dynamic generation of evaluation benchmarks tailored to specific corpora and use cases."
sources: ["from-local-to-global-graphrag"]
related: [llm-as-a-judge, graphrag]
---

# Adaptive Benchmarking

The process of dynamically generating evaluation benchmarks tailored to specific domains or use cases.

## Contrast with Static Benchmarks

| Static Benchmark | Adaptive Benchmarking |
|-----------------|----------------------|
| Fixed questions | Corpus-specific questions |
| Generic | Tailored to domain |
| May not match use case | Aligned with real usage |

## GraphRAG's Approach

To evaluate [[graphrag]] on global sensemaking:

1. **Corpus Description**: Brief description of dataset
2. **Persona Generation**: LLM generates K hypothetical users
3. **Task Identification**: For each user, N tasks they'd use RAG for
4. **Question Generation**: M questions per (user, task) pair

### Configuration

- K = 5 potential users
- N = 5 tasks per user
- M = 5 questions per (user, task)
- Total: 125 test questions per dataset

## Example

| Dataset | User | Task | Question |
|---------|------|------|----------|
| Podcast | Tech journalist | Understand tech trends | How do guests perceive privacy laws impact? |
| News | Educator | Health curriculum | What health topics for education? |

## Rationale

- Questions are **global** (require corpus-wide understanding)
- Don't require **specific low-level facts**
- Aligned with **real-world usage**

## Related Concepts

- [[llm-as-a-judge]] - How answers are evaluated
- [[graphrag]] - System being benchmarked
- [[query-focused-summarization]] - Task being evaluated