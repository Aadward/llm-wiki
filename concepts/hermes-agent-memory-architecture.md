---
title: Hermes Agent 记忆层级与边界——四层架构的深度解析
created: 2026-05-13
type: concept
tags: [agent, hermes, memory, architecture, memory-manager, layers, boundaries]
sources: [agent/memory_manager.py, agent/context_compressor.py, agent/prompt_builder.py]
confidence: high
---

# Hermes Agent 记忆层级与边界——四层架构的深度解析

## 一、记忆系统的核心设计问题

设计一个 Agent 记忆系统，要解决的不只是"存什么"，更重要的是：

1. **每层的容量是多少？** — 超出容量怎么处理
2. **每层的生命周期是什么？** — 谁创建，谁删除，何时失效
3. **各层之间的边界在哪？** — 什么时候用哪层，不重复不遗漏
4. **注入 vs 查询的区别** — 某些层是"每次都注入"，某些层是"需要才查询"

Hermes 的四层记忆架构，就是围绕这四个问题展开的。

---

## 二、四层记忆架构详解

### L1: Working Memory（工作记忆）

**载体：** 当前模型的 context window

**生命周期：** 当前会话，结束即消失

**容量：** 受限于 context window（通常是 128K-1M tokens）

**行为特征：**
- **注入方式：** 每次模型响应时，上下文窗口中的所有内容都是 Working Memory
- **无需显式管理：** 模型自然地在 context 中读写
- **溢出处理：** 触发 context compressor，将早期对话压缩为 summary（见 [[hermes-agent-message-loop]]）

**典型内容：**
- 当前对话的完整历史
- 当前任务的中间推理结果
- 正在进行的任务状态

**设计意图：** 模拟人类认知中的"工作台"——处理当前任务时有足够的即时工作空间。

---

### L2: Episodic Memory（情景记忆）

**载体：** SQLite 数据库 + FTS5 全文检索

**生命周期：** 永久保存，直到显式删除

**容量：** 无硬性限制，受磁盘空间约束

**注入方式：** 每次对话结束后，`MemoryManager.sync_all()` 会把对话内容同步到各 provider

**检索方式：**

```python
# MemoryManager.prefetch_all() 在每轮对话前被调用
context = self._memory_manager.prefetch_all(user_message, session_id=session_id)
```

检索结果通过 `build_memory_context_block()` 包装后注入：

```python
# 输出格式
<memory-context>
[System note: The following is recalled memory context,
NOT new user input. Treat as authoritative reference data — ...]
{检索到的记忆内容}
</memory-context>
```

**典型内容：**
- 跨会话的事实（"用户项目用的是 Next.js 14"）
- 经验总结（"上次这样部署失败了，换个方法"）
- 偏好记录（"用户喜欢早晨发日报"）

**溢出处理：** 无硬性限制。FTS5 检索可以快速定位相关内容。

**设计意图：** 模拟人类认知中的"日记本"——记录跨时间的事件和知识，需要时检索，不需要时安静躺着。

---

### L3: MEMORY.md（环境记忆）

**载体：** `~/.hermes/MEMORY.md` 文件

**生命周期：** 永久保存，手动编辑

**容量：** 当前 2200 字符（2.2KB 左右）

**注入方式：** 每次对话开始时，`prompt_builder.build()` 会将 MEMORY.md 内容注入到 system prompt 的 `MEMORY AND FACTS` section

**注入位置：** 在系统提示词中属于"静态事实"区块，与 Skills Index 同级

**典型内容：**
- 环境配置（"WSL 环境下，Windows 文件系统在 /mnt/c/"）
- 项目结构（"项目根目录是 ~/projects/acme"）
- 经验规则（"部署前先检查 .env"）

**溢出处理：** 容量有上限（~2200 字符）。超出后需要手动清理或归档。

**设计意图：** 模拟人类认知中的"笔记本"——记录重要的环境信息，不需要每次都检索，而是作为相对静态的背景知识始终注入。

---

### L4: USER.md（用户画像）

**载体：** `~/.hermes/USER.md` 文件

**生命周期：** 永久保存，手动编辑

**容量：** 当前 1375 字符（1.4KB 左右）

**注入方式：** 每次对话开始时注入，与 MEMORY.md 同区块

**注入位置：** 在系统提示词中属于"USER PROFILE" section

**典型内容：**
- 用户职业和角色（"用户是全栈工程师"）
- 偏好和习惯（"用户喜欢用中文交流"）
- 目标和动机（"用户想用 AI 提升开发效率"）

**溢出处理：** 同样有容量上限。

**设计意图：** 模拟人类认知中的"自我认知"——了解自己是谁、需要什么。所有回复都应该符合这个画像。

---

## 三、四层的对比矩阵

| | L1 Working Memory | L2 Episodic Memory | L3 MEMORY.md | L4 USER.md |
|---|---|---|---|---|
| **载体** | Context Window | SQLite + FTS5 | Markdown 文件 | Markdown 文件 |
| **生命周期** | 会话级 | 永久 | 永久 | 永久 |
| **容量** | Context window 限制 | 无限制 | ~2.2KB 软限制 | ~1.4KB 软限制 |
| **注入方式** | 隐式（整个 context） | 显式检索后注入 | 每次会话注入 | 每次会话注入 |
| **谁来填充** | 模型自动 | MemoryManager | 手动 + 自动摘要 | 手动 |
| **检索方式** | 无需检索 | FTS5 全文搜索 | 无需检索 | 无需检索 |
| **典型内容** | 当前对话、中间状态 | 跨会话事实、经验 | 环境配置、项目结构 | 用户画像、偏好 |

---

## 四、关键设计决策

### 4.1 注入 vs 查询的分离

**MEMORY.md 和 USER.md 是"注入"：**
- 每次会话开始时无条件注入
- 模型直接看到内容，不需要额外动作
- 代价是每次都消耗 token

**L2 Episodic Memory 是"查询"：**
- 只有当 `prefetch_all()` 检索到相关内容时才注入
- 通过 FTS5 精准匹配，而非全部注入
- 代价是有可能检索不到相关内容

**这个分离是有意为之的：**
- MEMORY.md/USER.md 是"必须始终知道"的信息（环境、用户）
- Episodic Memory 是"可能相关"的信息（过去的经验、事实）

### 4.2 `memory_context` 围栏机制

所有从 L2 检索到的内容都被包裹在 `<memory-context>...</memory-context>` 标签中：

```python
# memory_manager.py
def build_memory_context_block(raw_context: str) -> str:
    return (
        "<memory-context>\n"
        "[System note: The following is recalled memory context, "
        "NOT new user input. Treat as authoritative reference data — "
        "this is the agent's persistent memory and should inform all responses.]\n\n"
        f"{clean}\n"
        "</memory-context>"
    )
```

这个围栏有两个作用：

1. **视觉标记：** 模型能区分"记忆检索的内容"和"新输入"
2. **Streaming 时防泄漏：** `StreamingContextScrubber` 会在流式输出时剔除 `<memory-context>` 标签内的内容，防止记忆内容泄露到用户可见的回复中

### 4.3 容量软限制的来源

MEMORY.md (~2200 chars) 和 USER.md (~1375 chars) 的容量限制不是代码强制的，而是：

- **实际约束：** system prompt 本身有长度限制，加上 Skills Index、工具描述等，留给 memory 的空间有限
- **设计意图：** 鼓励用户保持记忆文件简洁，只存最重要的信息

这与"只记关键事实"的认知科学原则一致。

### 4.4 为什么 L2 用 SQLite + FTS5 而不是向量数据库

L2 使用 SQLite FTS5 而不是向量 embedding 检索，原因可能是：

1. **够用的精度：** FTS5 的 BM25 算法对"精确关键词匹配"效果很好，而这正是记忆检索的主要场景
2. **简单部署：** 不需要额外的向量服务
3. **可解释性：** 匹配结果就是原文，可审计

但 FTS5 有语义局限——它无法理解"我想起上次遇到的类似问题"这种模糊匹配。这由 `session_search` 工具补偿（用户可以主动搜索历史对话）。

---

## 五、边界问题：什么时候用哪层？

### 5.1 选择流程

```
需要记住 X，要不要存在记忆里？
    ↓
X 是关于什么的？
    ├─ 用户画像（偏好、习惯）→ USER.md
    ├─ 环境配置（项目结构、工具）→ MEMORY.md
    ├─ 跨会话事实（上次做了什么、什么失败了）→ L2 Episodic Memory
    └─ 当前任务状态 → Working Memory（L1）
```

### 5.2 容量满了怎么办？

| 层 | 满了怎么处理 |
|---|---|
| L1 Working Memory | context_compressor 压缩旧内容为 summary |
| L2 Episodic Memory | 无硬性限制，但检索会变慢（可清理旧记录） |
| L3 MEMORY.md | 手动删除不重要内容，或归档到笔记工具 |
| L4 USER.md | 手动删除不重要内容 |

**关键：** L3/L4 的容量限制需要用户主动管理，没有自动淘汰机制。

---

## 六、与 OpenClaw 的记忆系统对比

| | Hermes | OpenClaw |
|---|---|---|
| **L2 存储** | SQLite + FTS5 | SQLite + 语义搜索 |
| **L3/L4** | MEMORY.md / USER.md | Memory Blocks |
| **检索方式** | FTS5 关键词 + session_search 工具 | 语义相似度 |
| **用户画像** | USER.md（文件） | Memory Blocks（结构化） |
| **可扩展性** | 一个外部 Provider | Memory Blocks 数量可配置 |
| **记忆注入** | `<memory-context>` 围栏 | 直接注入 |

**最关键差异：** Hermes 的记忆是"模型主动通过围栏感知"，OpenClaw 的记忆是"系统直接注入"。前者更可控，后者更无缝。

---

## 七、知识断层

1. **外部 Provider 的行为：** 如果配置了外部 memory provider（如向量数据库），L2 和外部 provider 的边界在哪里？两者是否重叠？
2. **L2 的自动老化：** Episodic Memory 的记录是否有自动清理机制（如超过 N 个月的记录自动归档）？
3. **记忆冲突解决：** 如果 MEMORY.md 和 L2 Episodic Memory 中的内容矛盾，以哪个为准？

---

## 相关概念

- [[hermes-agent-memory-architecture]] — 原始四层架构概述
- [[hermes-agent-message-loop]] — L1 Working Memory 的管理机制（context compressor）
- [[hermes-agent-skill-nature]] — Skill 的存储方式与记忆系统的关系
- [[fts5-full-text-search]] — FTS5 检索原理
- [[memory-knowledge-systems]] — 通用记忆系统设计