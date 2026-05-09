---
title: OpenClaw ↔ Hermes 成本优化策略对比
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [openclaw, hermes, cost, optimization, token, auxiliary-models, prompt-cache]
sources: [concepts/hermes-agent-best-practices.md, concepts/openclaw-v2026-4-5-release.md, concepts/hermes-agent-message-loop.md]
confidence: medium
---

# OpenClaw ↔ Hermes 成本优化策略对比

## 一、两个系统的成本结构不同

| | Hermes 成本痛点 | OpenClaw 成本痛点 |
|---|---|---|
| **按 token 计费** | Skills Index 随技能库增长（~20 tokens × skill 数） | JSONL 日志无限膨胀，占 context window |
| **每次调用固定成本** | 技能越多，固定 overhead 越大 | Prompt Cache 命中率影响每次调用代价 |
| **优化方向** | 每次调用的 token 量 | 重复 prompt 的命中率 |

---

## 二、Hermes 的 Auxiliary Models（最有价值的策略）

### 2.1 三模型按场景路由

```
主任务（复杂推理）→ GPT-5.4 / Claude Opus
边角任务（摘要/提取）→ Qwen-Turbo / DeepSeek-V3
图像分析 → Qwen-VL
辅助任务 → MiniMax
```

**实测节省 40-60%**。边角任务（摘要、分类、提取）不需要最强模型，按场景路由后只花冤枉钱。

### 2.2 Auxiliary Models 配置

```yaml
auxiliary:
  vision:
    provider: openrouter
    model: qwen-vl-max
  compression:
    provider: openrouter
    model: deepseek-chat
```

### 2.3 前提条件

Auxiliary Models 策略依赖任务可清晰拆分为"复杂"和"简单"两部分。**如果任务强耦合，拆分本身可能很昂贵。**

---

## 三、OpenClaw 的 Prompt Cache

```
改善 Claude/GPT provider 的缓存命中率
减少重复 token 消耗
```

**关键问题**：Prompt Cache 缓存粒度是什么？如果 Skills 变更后缓存失效，重新填充的代价有多大？文档未明确说明。

---

## 四、日志膨胀问题的不同解决

| | Hermes | OpenClaw |
|---|---|---|
| **问题** | Skills Index 随技能库增长 | JSONL 日志无限膨胀 |
| **解决** | Context Compressor（压缩历史消息） | JSONL compaction（定期压缩） |
| **压缩对象** | 对话历史（messages list） | 执行日志（JSONL 文件） |
| **效果** | context window 内保留更多信息 | 减少存储和 context 占用 |

**本质区别**：Hermes 优化 token 成本，OpenClaw 优化存储和 context 占用。

---

## 五、定时任务的成本优化

| | Hermes | OpenClaw |
|---|---|---|
| **定时任务用轻量模型** | ✅ DeepSeek-V3 性价比高 | 未明确说明 |
| **Auxiliary Models** | ✅ 主任务/边角任务分离 | ❌ 无对应设计 |
| **Prompt Cache** | 部分 | ✅ 改善了命中率 |

**OpenClaw 缺少 Auxiliary Models 对应设计**——定时任务是否只能用同一个模型？文档没有说明。

---

## 六、知识断层清单

1. **Prompt Cache 缓存粒度**：Skills 变更后缓存是否失效？重新填充代价？
2. **Context Compressor 压缩质量**：LLM 摘要是否丢失关键上下文？
3. **任务拆分的实际代价**：Auxiliary Models 的任务路由本身是否有成本？
4. **多模型路由的维护成本**：配置多个 provider 的复杂度 vs 节省的 token 成本
5. **OpenClaw Auxiliary Models**：定时任务是否只能用同一个模型？

---

## 相关概念

- [[hermes-agent-best-practices]] — Hermes 最佳实践（含 Auxiliary Models 详解）
- [[openclaw-v2026-4-5-release]] — OpenClaw v2026.4.5（Prompt Cache 改善）
- [[hermes-agent-message-loop]] — Hermes 消息循环（Skills Index 累积成本）
