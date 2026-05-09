---
title: FTS5 全文检索原理（Hermes 记忆检索技术基础）
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [hermes, fts5, full-text-search, bm25, memory, sqlite, retrieval]
sources: [concepts/hermes-agent-source-code-architecture.md, concepts/hermes-agent-memory-architecture.md]
confidence: medium
---

# FTS5 全文检索原理（Hermes 记忆检索技术基础）

## 一、FTS5 在 Hermes 中的位置

FTS5（Full-Text Search 5）是 SQLite 内置的全文检索引擎，在 Hermes 中服务于 **L2 Episodic Memory** 的检索：

```
用户输入
    ↓
FTS5 检索（SQLite FTS5 MATCH）
    ↓
提取相关记忆 → 加入 system prompt
```

**相关源码**：

```python
def search_memory(self, query: str, limit: int = 5):
    cursor = self.conn.execute(
        "SELECT * FROM memories_fts WHERE memories_fts MATCH ? LIMIT ?",
        (query, limit)
    )
    return cursor.fetchall()
```

---

## 二、与 OpenClaw SQLite-vec 的对比

| | Hermes | OpenClaw |
|---|---|---|
| **检索引擎** | FTS5 | SQLite-vec |
| **算法** | BM25（关键词排名） | 向量 + BM25 + MMR + temporal decay |
| **语义能力** | 无（依赖关键词匹配） | 有（向量语义检索） |
| **session_search 补偿** | ✅ 直接搜索历史对话内容 | 无明确说明 |

**关键差异**：Hermes 用 BM25 做关键词匹配，OpenClaw 在此基础上加了向量语义检索。

---

## 三、BM25 的意义与局限

### BM25 能做到

- **词频加权**：一个词在文档中出现越多越相关
- **逆文档频率**：常见词（如"的"）权重降低
- **长文档惩罚**：文档越长，匹配同一个词的权重越低

### BM25 的局限

- **不理解语义**："狗"和"犬"是完全不同的词
- **同义词失效**：用户用不同词描述同一事物时可能召回失败
- **歧义容忍度低**：拼写错误/简繁体差异可能导致召回失败

---

## 四、FTS5 在 Hermes 记忆检索流程中的位置

```
用户输入
    ↓
Step 2: _retrieve_memory() — FTS5 检索相关记忆
    ↓
相关记忆内容 → 拼接到 system prompt
    ↓
模型基于完整上下文生成响应
```

---

## 五、session_search 工具的补偿作用

```bash
# 直接搜索历史对话内容，不依赖关键词 FTS5
hermes session_search "部署"
```

`session_search` 可以弥补 FTS5 语义不足的问题——它直接对历史对话做更广泛的搜索，不限于 FTS5 的 BM25 匹配。

---

## 六、知识断层清单

1. **FTS5 分词策略**：是否支持中文分词？（按空格/标点 vs jieba 等中文分词器）
2. **与 session_search 的关系**：`session_search` 是否走 FTS5，还是不同的检索路径？
3. **FTS5 性能上限**：记忆数据量到多大时 FTS5 查询开始变慢？是否有分页/限制机制？

---

## 相关概念

- [[hermes-agent-memory-architecture]] — Hermes 四层记忆架构
- [[memory-systems-comparison]] — 记忆系统对比（含检索策略对比）
- [[hermes-agent-message-loop]] — 消息循环中 `_retrieve_memory()` 的位置
