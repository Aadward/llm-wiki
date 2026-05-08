---
title: Memory & Knowledge Systems
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [memory, workflow]
sources: [raw/articles/openclaw-cookbook-2026-02.md]
confidence: high
---

# Memory & Knowledge Systems

OpenClaw 的记忆与知识管理方案 — 从简单的 Markdown 持久化到向量语义搜索。

## Layer 1: Built-in Markdown Memory（零成本起步）

OpenClaw 原生记忆系统是 `memory/` 目录下的 Markdown 文件，累积式永久存储。

**用法 — 就像发短信：**
```text
"Hey, remind me to read 'Designing Data-Intensive Applications'"
"Save this link: https://example.com/article"
"John recommended that restaurant on 5th street"
```

**特点：**
- 累积永久存储，说得越多记得越多
- 无需组织 — 不需要文件夹、标签
- [[openclaw]] 内置，无需安装

**局限：** 无搜索功能。记忆增长后只能 grep 关键词，无法语义检索。

## Layer 2: Semantic Memory Search（向量搜索增强）

工具：[memsearch](https://github.com/zilliztech/memsearch) — 在 Markdown 记忆文件上叠加向量语义搜索。

**安装与使用：**
```bash
pip install memsearch
memsearch config init
memsearch index ~/wiki/memory/        # 一次性索引
memsearch watch ~/wiki/memory/         # 文件变化自动重索引
```

**特性：**
- SHA-256 内容哈希 — 未变化文件永不重复 embedding，节省 API 费用
- Hybrid search（dense vectors + BM25 full-text）+ RRF reranking
- 支持 OpenAI、Google、Voyage、Ollama，或完全本地（无需 API key）
- Markdown 仍是唯一真相来源，向量索引只是派生缓存，任何时候可重建

## Layer 3: RAG Knowledge Base（URL 摄入）

Skill：[knowledge-base](https://clawhub.ai) + `web_fetch`

**工作流：**
1. 向 Telegram topic 或 Slack channel 丢 URL（文章、推文、YouTube、PDF）
2. Agent 抓取内容、分块、存储（附带 metadata: title、URL、date、type）
3. 语义查询："我存了什么关于 LLM memory 的内容？"

**配置 prompt：**
```text
When I drop a URL in the "knowledge-base" topic:
1. Fetch the content (article, tweet, YouTube transcript, PDF)
2. Ingest into the knowledge base with metadata
3. Reply with confirmation: what was ingested and chunk count

When I ask a question:
1. Search the knowledge base semantically
2. Return top results with sources and relevant excerpts
```

## Layer 4: Second Brain Dashboard（可视化检索）

**概念：** Agent 自动构建 Next.js 可视化 Dashboard，文本输入 → bot 记忆 → Dashboard 检索。

```text
I want to build a second brain system where I can review all our notes,
conversations, and memories. Please build that out with Next.js.

Include:
- A searchable list of all memories and conversations
- Global search (Cmd+K) across everything
- Clean, minimal UI
```

**核心洞察：**
- Capture = 发短信一样简单
- Retrieval = 搜索而非浏览
- 无文件夹、无标签、无复杂性

## Comparative Summary

| 方案 | 搜索能力 | 成本 | 复杂度 |
|------|---------|------|--------|
| 内置 Markdown Memory | ❌ 无（只能 grep） | $0 | 零 |
| memsearch 向量搜索 | ✅ 语义 | API 费用 | 低 |
| RAG Knowledge Base | ✅ 语义 + URL摄入 | API 费用 | 中 |
| Second Brain Dashboard | ✅ 可视化 + 语义 | API + 托管 | 高（Agent 构建） |

## Related

- [[openclaw]] — platform with built-in memory
- [[scheduled-automation]] — cron that syncs/procresses memory files
- [[multi-agent-orchestration]] — how agents share state via memory files