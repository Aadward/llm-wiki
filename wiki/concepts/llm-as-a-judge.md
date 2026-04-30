---
title: "LLM-as-a-Judge"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [evaluation, llm, benchmarking]
summary: "Evaluation methodology using an LLM to judge and compare outputs from different systems."
sources: ["from-local-to-global-graphrag"]
related: [graphrag, adaptive-benchmarking]
---

# LLM-as-a-Judge

An evaluation methodology where an LLM is used to judge and compare outputs from different systems.

## How It Works

1. **Input**: Question + two candidate answers (from competing systems)
2. **LLM Evaluation**: Given specific criteria, judge which answer is better
3. **Output**: Winner or tie indication

## Advantages

- Avoids need for human reference answers
- Scales to large evaluation sets
- Aligns with how humans perceive quality

## GraphRAG Evaluation

[[graphrag]] uses LLM-as-a-judge with three criteria:

### Comprehensiveness
> "How much detail does the answer provide to cover all aspects and details of the question?"

### Diversity
> "How varied and rich is the answer in providing different perspectives and insights on the question?"

### Empowerment
> "How well does the answer help the reader understand and make informed judgments about the topic?"

### Control: Directness
> "How specifically and clearly does the answer address the question?"

Used as reference to validate other results (directness is inherently in opposition to comprehensiveness/diversity).

## Reference

- First proposed by Zheng et al., 2024 (cited in [[source/from-local-to-global-graphrag]])
- Also known from MT-Bench benchmark

## Related Concepts

- [[adaptive-benchmarking]] - GraphRAG's approach to generating evaluation questions
- [[graphrag]] - System being evaluated