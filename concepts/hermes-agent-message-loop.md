---
title: Hermes AIAgent 消息循环深度解析
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [agent, hermes, source-code, architecture, message-loop, AIAgent, tool-dispatch]
sources: [concepts/hermes-agent-source-code-architecture.md]
confidence: high
---

# Hermes AIAgent 消息循环深度解析

## 一、同步循环的设计哲学

Hermes 的整个 Agent Loop 是**同步的（synchronous）**，没有 async/await：

```python
for iteration in range(self.max_iterations):  # 同步循环
    response = self._send_to_model(messages)   # 阻塞式 API 调用
    if not response.tool_calls:
        break
    tool_results = self._handle_tool_calls(response.tool_calls)
    # 同步等待每个工具返回
```

**这不是缺点，而是有意设计**：

| 考量 | 同步模型的优势 |
|------|--------------|
| **状态一致性** | 消息顺序严格有序，不需要锁或队列 |
| **推理容易** | 单线程循环，对话轨迹线性可追踪 |
| **工具依赖** | 先 pull 再 install——同步是唯一安全选择 |

**代价**：4 个工具并行调用的场景，同步模型串行等待，延迟 = sum(各工具耗时) 而非 max(各工具耗时)。

---

## 二、工具分发的命名空间设计

`toolset__tool_name` 双下划线命名法：

```
terminal__run    # flat: "terminal" + "run"，并非嵌套对象
file__read
web__search
```

**解决了两个问题**：
- **命名冲突**：两个 toolsets 都定义 `read` 时，`terminal__read` 和 `file__read` 不冲突
- **白名单简洁**：`enabled_toolsets: ["terminal", "file"]` 按 category 开关，无需逐工具列举

**代价**：工具名变长，每个 tool_call 多耗费几个 token。

---

## 三、并行工具调用的隐含依赖问题

当 `assistant` 消息包含**多个 tool_calls** 时，它们是**并行分发**的：

```
assistant: tool_calls = [
    {id: "call_A", function: {name: "web__search", ...}},
    {id: "call_B", function: {name: "terminal__run", ...}}
]
    ↓ 并行分发
tool: {tool_call_id: "call_A", content: "..."}   ← 无顺序保证
tool: {tool_call_id: "call_B", content: "..."}   ← 可能先于 A 返回
```

**关键风险**：如果 B 依赖 A 的结果（如 A 获取代码仓地址，B 在该地址执行 git clone），当前架构**没有显式依赖声明机制**。两个 tool_calls 同时分发，B 可能在 A 返回前就执行，导致失败。

**结论**：复杂依赖链任务必须靠模型在 tool_calls 序列中正确排序——模型需要自己理解"先 X 后 Y"的隐含依赖。

---

## 四、系统提示词的累积性膨胀

`_build_system_prompt()` 拼接 Skills Index：

```python
skills_index = self._get_skills_index()  # ~20 tokens per skill
parts.append(f"\n\n Available Skills:\n{skills_index}")
```

**累积成本**：

| 技能数量 | Skills Index tokens | 每次 API 调用额外成本 |
|----------|---------------------|---------------------|
| 10 | ~200 | 可忽略 |
| 50 | ~1,000 | 显著 |
| 200 | ~4,000 | 非常显著 |
| 500+ | ~10,000 | 可能触发 context 警告 |

Skills Index **每次 `_send_to_model()` 都注入**。这与"越用越聪明"的目标存在**潜在张力**——技能库增长，每次对话代价也在增长。

---

## 五、整轮提交的记忆保存模式

记忆保存时机：

```python
# Step 6: 整个对话只保存一次
if not self.skip_memory:
    self._save_to_memory(user_message, final_response)
```

**整轮提交（whole-turn commit）模式**。如果对话中途崩溃或中断，所有中间过程丢失。对比 OpenClaw 的 JSONL append-only 日志（每次工具调用都追加），Hermes 的设计更轻量，但中间状态无法恢复。

**后果**：超长对话（50 轮）中遇到问题，无法回退到前 30 轮。`/undo` 只撤销模型最后一条回复，无法撤销整个会话。

---

## 六、四个回调机制：文档最不透明的部分

| 回调 | 用途 | 文档透明度 |
|------|------|-----------|
| `clarify_callback` | 模型不确定时向用户确认 | 最低（仅参数名） |
| `approval_callback` | 危险操作需用户批准 | 少量（超时自动拒绝） |
| `sudo_callback` | 提升权限执行管理操作 | 无 |
| `progress_callback` | 报告任务进度 | 无 |

`approval_callback` 在最佳实践中有描述：危险命令审批超时后自动拒绝。但其他三个回调的触发时机、签名、返回值完全未知。

**这是 Hermes 的文档盲区**：回调机制为高级集成（自定义审批流程）而设计，但文档没有跟上。

---

## 七、消息循环完整流程图

```
用户输入
    ↓
Step 1: _build_system_prompt()
        ├── SOUL.md（人格）
        ├── AGENTS.md（指令）
        ├── CLAUDE.md（项目上下文）
        ├── 内置系统提示词
        └── Skills Index（~20 tokens × skill 数）
    ↓
Step 2: _retrieve_memory() — FTS5 检索相关记忆
    ↓
Step 3: 组装 messages = [system] + [检索到的记忆] + [user input]
    ↓
主循环（for iteration in range(max_iterations)）
    ↓
Step 5a: _send_to_model(messages) → response
    ↓ response.tool_calls?
    ├── 无 → final_response → Step 6 保存记忆 → Step 7 GEPA → 返回
    └── 有 → _handle_tool_calls()
              ↓
              并行分发到各 toolset
              ↓
              工具结果回填 messages
              ↓
              继续下一轮迭代
```

---

## 知识断层清单

1. **并行工具调用的依赖问题**：无显式依赖声明机制，模型如何正确排序隐含依赖链？
2. **`clarify_callback` / `sudo_callback` / `progress_callback` 完整行为**：签名、触发时机、返回值均未知
3. **Context Compressor 效果**：压缩后消息保留多少信息？摘要质量如何？
4. **Prompt Caching 缓存命中率**：Skills Index 每次不同（skill 新增/删除），缓存效率如何？
5. **max_iterations=90 的边界情况**：接近上限时的行为（强制终止？警告？）

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-source-code-architecture]] — 源码架构（含 AIAgent 60+ 参数详解）
- [[hermes-agent-learning-loop]] — GEPA 引擎与 Skill 生成机制
- [[hermes-agent-skills-system]] — 技能系统
- [[hermes-agent-memory-architecture]] — 四层记忆架构
