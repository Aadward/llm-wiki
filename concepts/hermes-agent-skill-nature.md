---
title: Skill 的本质——为什么不是 RAG
created: 2026-05-13
type: concept
tags: [agent, hermes, skill, rag, retrieval, knowledge-management]
sources: [agent/prompt_builder.py, tools/skill_manager_tool.py, agent/curator.py]
confidence: high
---

# Skill 的本质——为什么不是 RAG

## 一、核心结论先行

**Skill 不是 RAG。**

RAG（检索增强生成）基于向量相似度自动匹配相关内容，而 Skill 系统是基于**模型主动决策**的知识调用机制。两者的根本区别在于"谁来决定使用什么知识"——RAG 是算法自动匹配，Skill 是模型自主判断。

---

## 二、Skill 的技术架构

### 2.1 存储结构

```
~/.hermes/skills/
├── <skill-name>/
│   ├── SKILL.md           # 主文件（Markdown，含 YAML frontmatter）
│   ├── references/        # 参考资料
│   ├── templates/         # 模板文件
│   ├── scripts/           # 脚本文件
│   └── assets/            # 资源文件
└── .archive/              # Curator 归档目录（不参与检索）
```

### 2.2 两层注入机制（关键）

Skill 在系统提示词中以**两层方式**存在：

```
系统提示词
  ├── SKILL INDEX（始终注入）
  │     每个 Skill 只包含：name + description
  │     格式："- skill-name: 描述文字"
  │
  └── FULL SKILL CONTENT（按需加载）
        模型调用 skill_view(name) 后
        完整 SKILL.md 内容才注入到上下文
```

**注入时机：**

- **Index 层**：每个对话开始时构建并注入，扫描所有 `SKILL.md` 文件
- **Content 层**：模型主动调用 `skill_view(name)` 后才注入

### 2.3 条件可见性

Skill 支持条件激活，在 frontmatter 中声明：

```yaml
---
name: my-skill
description: "..."
conditions:
  requires_toolsets: [terminal, file]      # 必须有这些工具集才显示
  requires_tools: [git]                    # 必须有这些工具才显示
  fallback_for_toolsets: [safe]             # 当 safe 工具集可用时隐藏
---
```

`build_skills_system_prompt()` 会在构建索引时根据当前可用的工具集过滤 Skills。

---

## 三、为什么 Skill 不是 RAG

### 3.1 检索机制的对比

| | RAG | Hermes Skill |
|---|---|---|
| **检索方式** | 向量相似度（embedding similarity） | 描述匹配（description string match） |
| **触发者** | 算法自动 | 模型主动判断 |
| **匹配粒度** | 段落级别（chunk） | 整个 Skill 文件 |
| **相关性判断** | cosine similarity > threshold | 模型理解 description 文本后决定 |
| **上下文感知** | 差（脱离对话语境） | 强（在当前对话中判断） |
| **精确度** | 可能匹配无关内容 | 匹配更精准（模型理解） |

### 3.2 核心区别的本质

RAG 是**算法决策**：给定查询，用 embedding 在向量数据库中找到最相似的文本块。

Skill 是**智能决策**：模型阅读了所有 Skill 的 description（在 index 中），自主判断哪个 Skill 适用于当前任务，然后显式调用 `skill_view(name)` 加载完整内容。

**Skill 的本质是"模型知道什么时候该查哪个知识库条目"，而不是"算法猜测相关内容是什么"。**

### 3.3 模型视角的 Skill 使用流程

```
对话开始
  ↓
系统注入 Skill Index（所有 Skill 的 name + description）
  ↓
模型阅读用户问题
  ↓
模型判断："这个问题涉及 X，Skill 列表中有 X 相关的 Skill"
  ↓
模型调用 skill_view(name="X-skill")
  ↓
完整 Skill 内容注入到上下文
  ↓
模型按照 Skill 中的指引执行
```

---

## 四、Skill 存储"解题模式"而非"代码片段"的设计意图

### 4.1 代码片段方案的问题

如果 Skill 存储代码片段：

```python
# ❌ 这种方式的问题
SKILL.md:
  # 解决方案
  terminal(command="hermes update")
```

- **脆弱**：代码依赖具体环境、工具版本、路径
- **不可复用**：换一个环境可能就跑不通了
- **不可解释**：模型只知道"做什么"，不知道"为什么这样做"

### 4.2 解题模式方案的优势

Skill 存储解题思路（模式）：

```markdown
# Skill: hermes-update-workflow
description: "更新 Hermes Agent 到最新版本的标准流程"

## 触发条件
用户要求更新 Hermes 或遇到版本相关问题

## 操作步骤
1. 检查当前版本：`hermes --version`
2. 执行更新：`hermes update`
3. 验证安装：`hermes doctor`
4. 如遇问题，检查 .env 配置是否正确

## 最佳实践
- 更新前确认 .env 中的 API key 有效
- 大版本更新后运行 hermes doctor
```

**优势：**

- **环境无关**：描述性的步骤可以在不同环境下适配
- **可解释**：模型知道为什么这样做
- **可组合**：多个 Skill 可以同时加载，相互补充
- **可持续维护**：模式比代码片段更容易更新

### 4.3 "推迟编译到运行时"的真正含义

Hermes Skill 系统将"编译"（具体代码生成）推迟到运行时：

- Skill 是"解题思路"的描述
- 加载 Skill 后，模型根据当前上下文重新生成具体代码
- 这比 RAG 直接注入代码片段更灵活，因为生成的动作符合当前环境

**这与 Hermes "Engine-First"（推理优先于执行）的定位完全一致。**

---

## 五、Skill 数量膨胀问题

### 5.1 Index 层的成本

Skill Index 只注入 name + description，即使有 500 个 Skill，每次对话的额外 token 成本也只有约 10,000 token（每个约 20 token）。这是可控的。

### 5.2 Content 层的问题

当模型调用 `skill_view(name)` 时，完整的 SKILL.md 被注入。如果 Skill 内容很长，大量加载会导致上下文膨胀。

**解决方案：Curator 的伞形 Skill 合并**

Curator 的核心职责之一就是防止 Skill 数量膨胀：

```
❌ 100 个 narrow skills
    pr-fix-001: "修复 PR 中的拼写错误"
    pr-fix-002: "修复 PR 中的语法错误"
    pr-fix-003: "修复 PR 中的格式问题"
    ...

✅ 1 个伞形 Skill
    pr-workflow:
      ## 拼写检查
      ### 触发：...
      ### 操作：...
      ## 语法检查
      ### 触发：...
      ### 操作：...
      ## 格式规范
      ### 触发：...
      ### 操作：...
```

Curator 会主动将窄范围 Skill 合并为宽范围伞形 Skill，保持 Skill 库的可维护性。

---

## 六、Skill 与其他知识管理方案的对比

| | 存储形态 | 检索方式 | 适用场景 |
|---|---|---|---|
| **Skill** | Markdown 文件 | 模型主动加载 | 流程性、经验性知识 |
| **MEMORY.md** | 单个 Markdown | 每次对话注入 | 项目级事实、偏好 |
| **USER.md** | 单个 Markdown | 每次对话注入 | 用户画像 |
| **SQLite 会话历史** | SQLite + FTS5 | session_search 工具 | 搜索历史对话 |
| **RAG** | 向量数据库 | embedding 相似度 | 大规模文档检索 |
| **向量数据库** | 向量 + 原文 | cosine similarity | 大量技术文档 |

**关键洞察：** Skill 是唯一需要模型**主动决定**使用什么知识的机制。其他方案都是被动注入或自动检索。

---

## 七、Skill 系统的局限性

1. **依赖模型判断**：如果模型没有识别出应该加载某个 Skill，这个 Skill 就不会被使用
2. **没有自动发现**：RAG 可以通过相似度自动发现相关内容，Skill 需要模型"记得"去查
3. **窄场景优势明显**：对于"这个任务该用什么流程"这类问题，Skill 比 RAG 更精准；对于"这个文档里有什么"这类问题，RAG 更擅长

---

## 相关概念

- [[hermes-agent-learning-loop]] — Skill 的创建机制（GEPA 实际上是 LLM 自主决策）
- [[hermes-agent-memory-architecture]] — 四层记忆架构（Skill 是其中的一部分）
- [[hermes-agent-message-loop]] — 消息循环机制
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比
