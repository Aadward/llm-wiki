---
title: ReAct Loop 范式深度理解
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [react, loop, paradigm, agent, openclaw, hermes, reasoning, tool-use]
sources: [concepts/openclaw-source-code-architecture.md, concepts/hermes-agent-source-code-architecture.md]
confidence: high
---

# ReAct Loop 范式深度理解

## 一、ReAct 的核心思想

**ReAct = Reasoning + Acting**

两个系统的执行循环本质上都是 ReAct 的变体：

**OpenClaw 的 ReAct Loop：**
```
user input → plan → reason → call skill → observe → update memory
```

**Hermes 的消息循环（功能等价）：**
```
用户输入 → 检索记忆 → _send_to_model()
    → 有 tool_calls? → 分发工具 → 回填结果 → 继续循环
    → 无 tool_calls → 返回响应
```

---

## 二、ReAct 的核心洞见

**推理和执行应该交替进行，而非先推理再执行：**

- 环境反馈（工具返回结果）本身就是推理的一部分
- 计划可能在执行中发现错误，需要修正
- "先想好再做"在复杂环境中不现实

---

## 三、两个系统的 ReAct 实现对比

| | OpenClaw | Hermes |
|---|---|---|
| **Loop 可视性** | 显式 named `runtime_loop` 模块 | 隐式消息循环 |
| **循环控制** | `runtime_loop` 模块 | `max_iterations` 参数（默认 90） |
| **记忆更新** | 每步后 `update memory` | 对话结束时整轮提交 |
| **并行工具** | 无显式并行 | 并行分发（无依赖声明） |

---

## 四、ReAct 的根本性局限

**ReAct 没有显式依赖声明机制。**

现实任务经常有依赖树：
```
Step 1: 获取服务器地址
Step 2: SSH 连接到该服务器  ← 依赖 Step 1
Step 3: 在服务器上执行命令  ← 依赖 Step 2
```

两个系统都依赖模型自己正确序列化这个依赖链——但模型不理解"依赖"这个概念，只是学会了"先说 A 再做 B"的模式。

**结论**：ReAct 在依赖链复杂的场景下是不稳定的。两个系统都没有解决这个问题。

---

## 五、知识断层清单

1. **依赖树的解决方案**：是否有系统（HuggingGPT、AutoGPT 等）显式建模了依赖图？
2. **OpenClaw 的并行能力**：是否支持同一轮次内多个 skill 并行调用？
3. **ReAct vs Plan-and-Execute**： Hermes 的同步循环 vs 分离的计划-执行模式，哪个更适合什么场景？

---

## 相关概念

- [[openclaw-source-code-architecture]] — OpenClaw ReAct Loop 实现
- [[hermes-agent-message-loop]] — Hermes 消息循环（同步 Loop）
- [[hermes-agent-source-code-architecture]] — AIAgent 类设计
