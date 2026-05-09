---
title: OpenClaw ↔ Hermes 记忆系统深度对比
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [openclaw, hermes, memory, storage, fts5, sqlite, markdown, comparison]
sources: [concepts/hermes-agent-memory-architecture.md, concepts/openclaw-source-code-architecture.md, concepts/openclaw-hermes-comparison.md]
confidence: high
---

# OpenClaw ↔ Hermes 记忆系统深度对比

## 一、两种"记忆"的根本定义差异

**OpenClaw 的"记忆"**：纯 Markdown 文件 = 单一源真理（Source of Truth）

**Hermes 的"记忆"**：四层架构（Working → Episodic → MEMORY.md → USER.md）= 系统内部运行时状态

**这意味着**：
- Hermes 的记忆是**运行时状态**（SQLite 存储）
- OpenClaw 的记忆是**文件系统状态**（磁盘文件）

这个差异导致了所有后续的工程取舍。

---

## 二、存储策略对比

| | Hermes | OpenClaw |
|---|---|---|
| **主存储** | SQLite（运行时数据库） | Markdown 文件（磁盘） |
| **会话状态** | SQLite（hermes_state.db） | JSONL append-only 日志 |
| **可读性** | 二进制，需要工具查询 | 直接 `cat` 查看 |
| **版本控制** | 需要额外配置 | 原生 Git 友好 |
| **崩溃恢复** | 依赖 SQLite 事务 | 文件系统天然持久 |

**最实际差异**：OpenClaw 的记忆可直接 `cat ~/.openclaw/MEMORY.md` 查看，Hermes 需要 `sqlite3` 查询。OpenClaw 对学习和调试更透明。

---

## 三、检索策略对比

| | Hermes | OpenClaw |
|---|---|---|
| **检索引擎** | FTS5（SQLite 内置全文检索） | SQLite-vec（向量搜索插件） |
| **检索方式** | BM25 关键词匹配 | 向量 + BM25 + MMR + temporal decay |
| **语义能力** | 依赖模型理解关键词 | 原生向量语义检索 |
| **混合搜索** | 不支持 | 支持（vector + BM25 融合） |

**实际影响**：当用户说"上次我让你帮我部署的那个项目"，Hermes 需要"部署"这个关键词才能召回；OpenClaw 可能通过语义相似度直接召回。

**Hermes 的补偿**：`session_search` 工具可直接搜索历史对话内容，弥补了纯 BM25 的语义不足。

---

## 四、持久化策略对比

| | Hermes | OpenClaw |
|---|---|---|
| **写入时机** | 对话结束时整轮提交 | 每次工具调用都追加 |
| **写入内容** | 最终结果摘要 | 完整事件流 |
| **崩溃恢复** | 可能丢失中间状态 | 最多丢最后一次工具结果 |
| **存储增长** | 慢（只存结果） | 快（完整日志需定期 compaction） |

**本质取舍**：在"信息完整性"和"存储成本"之间的取舍。Hermes 选择存储少但可能丢中间过程，OpenClaw 选择存完整但需要管理日志增长。

---

## 五、用户画像的不同哲学

| | Hermes | OpenClaw |
|---|---|---|
| **用户画像** | 独立文件 `USER.md` | 混在 `MEMORY.md` 中 |
| **编辑方式** | 用户可直接编辑文件 | 通过对话让 Agent 更新 |
| **可迁移性** | 用户画像可独立迁移 | 与记忆混在一起 |
| **多用户隔离** | 依赖 Profiles 多实例 | 依赖 Session 绑定 |

---

## 六、记忆的"主动性"：两种哲学的根本对立

| | Hermes | OpenClaw |
|---|---|---|
| **假设** | 系统应该自动管理记忆 | 人类应该直接掌控记忆 |
| **更新方式** | 系统自动总结，人类不操心 | 人类可直接 vim 编辑 Markdown |
| **控制粒度** | 通过 API 间接控制 | 文件即控制界面 |
| **适合用户** | "系统帮我管"型 | "我随时能插手"型 |

**这决定了使用体验的基调**：喜欢当"甩手掌柜"的用户倾向 Hermes；喜欢"我随时能插手"的用户倾向 OpenClaw。

---

## 七、JSONL Append-only 的深层含义

OpenClaw 的 sessions/store.ts 是一个容易被低估的设计：

```
新事件 → 追加到日志文件 → 定期 compaction → 压缩旧日志
```

这带来几个实际能力：
1. **审计能力**：任何操作都有时间戳记录
2. **回放能力**：给定一个 session_id，可以回放整个对话过程
3. **并发安全**：append-only 天然避免读写冲突

**这是 Hermes 完全缺失的能力**：Hermes 的整轮提交模式无法支持"回放某个会话"这样的审计需求。

---

## 知识断层清单

1. **FTS5 的分词策略**：是否支持中文分词？
2. **SQLite-vec 的向量维度**：嵌入模型是什么？维度多少？
3. **temporal decay 的衰减公式**：多久以前的记忆会被降权？
4. **OpenClaw compaction 触发条件**：文件多大时触发？保留多少历史？
5. **Hermes L2 和 L3 的边界**：什么信息存 SQLite？什么存 MEMORY.md？
6. **OpenClaw 是否有 episodic memory 概念**：还是完全依赖 Semantic Search？

---

## 相关概念

- [[hermes-agent-memory-architecture]] — Hermes 四层记忆架构详解
- [[openclaw-source-code-architecture]] — OpenClaw 记忆系统详解
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 完整对比
