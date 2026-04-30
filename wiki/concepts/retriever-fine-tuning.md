---
title: "Retriever Fine-tuning"
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [rag, retriever, fine-tuning, embedding]
summary: "Fine-tuning methods for dense retrievers including SFT (contrastive learning), LSR (LM-supervised), and Adapter methods for domain adaptation."
sources: ["modular-rag-transforming-rag-systems"]
related: [modular-rag, retrieval]
---

# Retriever Fine-tuning

Methods for optimizing retriever performance in RAG systems.

## Methods

### 1. Supervised Fine-Tuning (SFT)

Fine-tune with labeled domain data using contrastive learning.

**Loss Function**:
```
L(DR) = -1/T Σ log(e^(sim(qᵢ,dᵢ⁺)) / (e^(sim(qᵢ,dᵢ⁺)) + Σ e^(sim(qᵢ,dᵢ⁻)))
```

- Reduce distance: positive samples (qᵢ, dᵢ⁺)
- Increase distance: negative samples (qᵢ, dⱼ⁻)

### 2. LM-Supervised Retriever (LSR)

Use LLM-generated outputs as supervisory signals.

**Formula**:
```
P_LSR(d|q,y) = e^(P_LM(y|d,q)/β) / Σ e^(P_LM(y|d,q)/β)
```

### 3. Adapter

Add trainable adapter module when direct fine-tuning is costly.

**Benefits**:
- Avoids fine-tuning large API-based models (e.g., OpenAI Ada-002)
- Enables task-specific adaptation

### 4. LLM Reward RL

Use LLM output as reward for reinforcement learning alignment.

## Comparison

| Method | Data | Cost | Use Case |
|--------|------|------|----------|
| SFT | Labeled pairs | Medium | Domain-specific |
| LSR | LLM outputs | Low | No labeled data |
| Adapter | Light fine-tune | Low | API models |
| RL | LLM/human feedback | High | Alignment |

## Reference

- [[source/modular-rag-transforming-rag-systems]] (Section IV-C2)
