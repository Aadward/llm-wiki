---
title: Hermes Agent 源码架构深度解析
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, source-code, architecture, AIAgent, tool-dispatch]
sources: [web/blog.csdn.net/2401_85375151, web/blog.csdn.net/henrylin9999, web/cloud.tencent.com/developer/article/2655385]
confidence: high
---

# Hermes Agent 源码架构深度解析

## 概述

本文深入剖析 Hermes Agent 的核心源码架构，从入口分层、核心 AIAgent 类、工具系统、消息循环、数据流转等方面完整呈现其工程设计。

**一句话总结**：Hermes Agent 是一个以 `run_agent.py` 中 `AIAgent` 类为心脏、以工具注册中心为四肢、以消息网关为神经末梢的单进程、自进化、多模态 Agent 运行时。

---

## 一、系统全景分层架构

```
┌─────────────────────────────────────────────┐
│                  入口层                      │
│   CLI (cli.py) │ Gateway │ Cron Scheduler   │
└─────────────────┬───────────────────────────┘
                   ▼
┌─────────────────────────────────────────────┐
│              运行时解析层                     │
│   hermes_cli/runtime_provider.py            │
│   hermes_cli/auth.py                       │
└─────────────────┬───────────────────────────┘
                   ▼
┌─────────────────────────────────────────────┐
│              核心 Agent 引擎                 │
│   run_agent.py → AIAgent.__init__()        │
│   agent/prompt_builder.py                   │
│   agent/prompt_caching.py                  │
│   agent/context_compressor.py              │
└─────────────────┬───────────────────────────┘
                   ▼
┌─────────────────────────────────────────────┐
│              工具系统（Toolsets）            │
│   内置 + MCP 扩展 + 动态注册                 │
└─────────────────┬───────────────────────────┘
                   ▼
┌─────────────────────────────────────────────┐
│              持久化层                         │
│   hermes_state.py / gateway/session.py      │
│   SQLite + Markdown Files                  │
└─────────────────────────────────────────────┘
```

---

## 二、入口层详解

### 2.1 CLI 入口（cli.py）

CLI 是用户最常用的入口，职责：
- 解析命令行参数（`hermes chat`、`hermes setup`、`hermes config` 等）
- 初始化运行时环境
- 调用 `AIAgent` 驱动对话
- 通过 Rich/TUI 渲染输出（reasoning 过程、streaming 响应）

```bash
# 核心命令
hermes chat                    # 交互式对话
hermes chat -q "问题"          # 单次问答
hermes setup                   # 初始化配置
hermes config set <key> <val>  # 修改配置
```

### 2.2 Gateway 入口（gateway/run.py）

Gateway 负责消息平台的统一接入：
- 适配多种消息平台（Telegram、Discord、Slack 等）
- 将外部消息格式转换为内部统一格式
- 维护会话状态
- 通过 `adapter.send()` 推送响应

```python
# Gateway 消息流转
平台消息 → Adapter 解析 → 统一 Message 格式 → AIAgent 处理
                                                    ↓
平台响应 ← Adapter 封装 ← AIAgent 返回 ──────────────┘
```

### 2.3 Cron 入口（cron/scheduler.py）

定时任务入口：
- 解析 cron 表达式
- 调度 AIAgent 执行定时任务
- 支持 `autoconversation()` 自动触发对话
- 输出结果到文件或推送到消息平台

---

## 三、核心引擎：AIAgent 类

### 3.1 类定位

`run_agent.py` 中的 `AIAgent` 是整个系统的心脏，采用**大类单文件**设计（所有核心逻辑汇聚于一个文件），保证了核心循环的内聚性和可追踪性。

### 3.2 构造器参数体系（60+ 参数）

```python
class AIAgent:
    def __init__(
        self,
        # ── 模型配置 ──
        model: str = "anthropic/claude-opus-4.6",
        api_key: str = None,
        base_url: str = "https://openrouter.ai/api/v1",
        max_tokens: int = 4096,
        temperature: float = 0.7,

        # ── 循环控制 ──
        max_iterations: int = 90,     # 最大迭代次数，防止死循环
        max_turns: int = 20,          # 最大对话轮次

        # ── 工具控制 ──
        enabled_toolsets: list = None,   # 白名单：只启用这些 toolset
        disabled_toolsets: list = None,   # 黑名单：禁用这些 toolset

        # ── 行为控制 ──
        quiet_mode: bool = False,         # 静默模式，减少输出
        save_trajectories: bool = False,  # 保存轨迹（用于调试）

        # ── 上下文标识 ──
        platform: str = None,   # "cli", "telegram", "discord" ...
        session_id: str = None,

        # ── 上下文加载控制 ──
        skip_context_files: bool = False,  # 跳过 CLAUDE.md / AGENTS.md
        skip_memory: bool = False,         # 跳过记忆检索

        # ── 回调机制（四大回调） ──
        clarify_callback=None,   # 澄清回调：模型不确定时向用户确认
        approval_callback=None,   # 审批回调：危险操作需用户批准
        sudo_callback=None,      # 提权回调：提升权限执行管理操作
        progress_callback=None,  # 进度回调：报告任务进度

        # ── 记忆配置 ──
        memory_backend: str = "sqlite",  # 或 "memory"
        memory_path: str = "~/.hermes/memory",

        # ── 技能配置 ──
        skills_path: str = "~/.hermes/skills",
        auto_skill_generation: bool = True,

        # ── MCP 配置 ──
        mcp_servers: list = None,
        mcp_allowed_tools: list = None,  # 白名单模式

        # ── 输出配置 ──
        output_format: str = "markdown",  # 或 "text"
        streaming: bool = True,
        # ... 共计 60+ 参数
    ):
```

### 3.3 核心方法

| 方法 | 职责 |
|------|------|
| `run_conversation()` | 同步 Agent 主循环，消息 exchange 的外层包装 |
| `_run_single_turn()` | 单轮对话处理：发送 → 接收 tool_calls → 分发工具 → 回填结果 |
| `_send_to_model()` | 将消息列表发送给 LLM，支持流式 |
| `_handle_tool_calls()` | 工具调用分发：解析 tool_calls → 调用对应工具 → 返回结果 |
| `_build_system_prompt()` | 装配系统提示词：SOUL.md + AGENTS.md + 上下文文件 |
| `_load_context_files()` | 加载 CLAUDE.md、AGENTS.md、SOUL.md 等上下文 |
| `_retrieve_memory()` | 从 SQLite FTS5 检索相关记忆 |
| `_save_to_memory()` | 写入情景记忆到 SQLite |
| `_generate_skill()` | GEPA 引擎触发技能自动生成 |
| `_evaluate_and_reflect()` | GEPA 的 E 阶段：评估任务完成质量 |

---

## 四、工具系统与注册中心

### 4.1 工具系统架构

```
Toolset（工具集）
├── terminal     # 执行 shell 命令
├── file         # 文件读写操作
├── browser      # 浏览器自动化
├── web          # 网页搜索/爬取
├── code_exec    # Python/JS 代码执行
├── mcp          # MCP 协议扩展
└── ...（20+ 内置 toolsets）
```

### 4.2 工具注册流程

```python
# 工具注册发生在 AIAgent 初始化阶段
def _register_default_toolsets(self):
    # 1. 发现所有内置 toolset
    for toolset_name in self._discover_toolsets():
        toolset = self._load_toolset(toolset_name)
        # 2. 按 enabled/disabled 过滤
        if self.enabled_toolsets and toolset_name not in self.enabled_toolsets:
            continue
        if toolset_name in self.disabled_toolsets:
            continue
        # 3. 注册到工具表
        self._tools[toolset_name] = toolset

# 工具表结构
self._tools = {
    "terminal": ToolsetInstance(...),
    "file": ToolsetInstance(...),
    ...
}
```

### 4.3 工具分发（Tool Dispatch）

```python
def _handle_tool_calls(self, tool_calls: list) -> list:
    """
    模型返回 tool_calls 时，解析并分发到对应 toolset
    """
    results = []
    for tool_call in tool_calls:
        tool_name = tool_call["function"]["name"]
        arguments = json.loads(tool_call["function"]["arguments"])

        # 1. 解析工具名格式：toolset_name__tool_name
        if "__" in tool_name:
            toolset_name, actual_tool_name = tool_name.split("__", 1)
        else:
            toolset_name, actual_tool_name = "default", tool_name

        # 2. 查找工具
        toolset = self._tools.get(toolset_name)
        if not toolset:
            results.append({"error": f"Unknown toolset: {toolset_name}"})
            continue

        # 3. 执行工具（带超时和错误处理）
        try:
            result = toolset.call(actual_tool_name, **arguments)
            results.append({"tool_call_id": tool_call["id"], "output": result})
        except Exception as e:
            results.append({"tool_call_id": tool_call["id"], "error": str(e)})

    return results
```

### 4.4 消息格式（OpenAI 兼容）

```python
# 消息以 OpenAI 格式存储
messages = [
    {"role": "system",      "content": "你是一个有用的助手"},
    {"role": "user",        "content": "搜索 Python 教程"},
    {"role": "assistant",   "content": None,
     "tool_calls": [
         {"id": "call_abc123", "type": "function",
          "function": {"name": "web__search",
                       "arguments": '{"query": "Python tutorial"}'}}
     ]},
    {"role": "tool", "tool_call_id": "call_abc123",
     "content": "Python 教程结果..."}
]
```

---

## 五、同步 Agent Loop 流程

### 5.1 完整消息循环

```python
async def run_conversation(self, user_message: str) -> str:
    """
    同步 Agent Loop 的完整流程
    """

    # Step 1: 加载上下文文件（SOUL.md / AGENTS.md / CLAUDE.md）
    system_prompt = self._build_system_prompt()

    # Step 2: 检索记忆（Episodic Memory）
    if not self.skip_memory:
        relevant_memories = self._retrieve_memory(user_message)
        system_prompt += "\n\n Relevant memories:\n" + relevant_memories

    # Step 3: 初始化消息列表
    messages = [{"role": "system", "content": system_prompt}]

    # Step 4: 追加用户消息
    messages.append({"role": "user", "content": user_message})

    # Step 5: 进入主循环（同步，非 async）
    for iteration in range(self.max_iterations):
        # 5a. 发送到 LLM
        response = self._send_to_model(messages)

        # 5b. 检查是否有 tool_calls
        if not response.tool_calls:
            # 最终响应，直接返回
            final_response = response.content
            break

        # 5c. 有 tool_calls → 分发工具
        tool_results = self._handle_tool_calls(response.tool_calls)

        # 5d. 将工具结果回填到消息列表
        for tr in tool_results:
            messages.append({
                "role": "tool",
                "tool_call_id": tr["tool_call_id"],
                "content": tr.get("output", tr.get("error", ""))
            })

        # 5e. 继续下一轮迭代

    # Step 6: 保存记忆
    if not self.skip_memory:
        self._save_to_memory(user_message, final_response)

    # Step 7: 评估是否生成技能（GEPA 的 E 阶段）
    if self.auto_skill_generation:
        self._evaluate_and_reflect(messages)

    return final_response
```

### 5.2 模型响应二分

```
LLM 响应
    ├── 无 tool_calls → 最终响应 → 返回用户
    └── 有 tool_calls → tool dispatch → tool result 回填 → 下一轮
```

---

## 六、系统提示词装配

### 6.1 `_build_system_prompt()` 流程

```python
def _build_system_prompt(self) -> str:
    parts = []

    # 1. SOUL.md（人格定义）
    if os.path.exists("SOUL.md"):
        parts.append(self._read_file("SOUL.md"))

    # 2. AGENTS.md（Agent 指令）
    if not self.skip_context_files and os.path.exists("AGENTS.md"):
        parts.append(self._read_file("AGENTS.md"))

    # 3. 项目级上下文（CLAUDE.md / AGENTS.md 在项目目录）
    if not self.skip_context_files:
        parts.extend(self._load_context_files())

    # 4. 内置系统提示词
    parts.append(self._get_base_system_prompt())

    # 5. 技能索引（所有可用技能的名称+描述，~20 tokens 每个）
    skills_index = self._get_skills_index()
    if skills_index:
        parts.append(f"\n\n Available Skills:\n{skills_index}")

    return "\n\n".join(parts)
```

### 6.2 优先级

```
AGENTS.md（最高优先级，项目级指令）
    ↓
SOUL.md（人格定义）
    ↓
CLAUDE.md（项目上下文）
    ↓
内置系统提示词
    ↓
Skills Index（最低优先级）
```

---

## 七、上下文管理

### 7.1 上下文压缩（Context Compression）

```python
# agent/context_compressor.py
class ContextCompressor:
    """
    当消息列表接近 context window 上限时，
    自动压缩历史消息，保留关键信息
    """
    def compress(self, messages: list, max_tokens: int) -> list:
        # 1. 计算当前 token 数
        # 2. 找出可压缩区间（早期对话）
        # 3. 对早期对话做摘要
        # 4. 替换原消息
```

### 7.2 Prompt 缓存（Prompt Caching）

Hermes 支持对系统提示词做缓存：
- `agent/prompt_caching.py` 负责管理
- 缓存 key = 系统提示词内容的 hash
- 命中缓存时跳过重复的 prompt 注入

---

## 八、数据持久化

### 8.1 SQLite 存储

```python
# hermes_state.py
class HermesState:
    """
    SQLite 数据库封装，管理会话状态和情景记忆
    """
    def __init__(self, db_path: str = "~/.hermes/hermes_state.db"):
        self.conn = sqlite3.connect(os.path.expanduser(db_path))
        self.conn.row_factory = sqlite3.Row

    def save_memory(self, session_id: str, content: str, memory_type: str):
        # memory_type: "episodic" | "semantic" | "working"
        self.conn.execute(
            "INSERT INTO memories VALUES (?, ?, ?, ?)",
            (session_id, content, memory_type, datetime.now())
        )

    def search_memory(self, query: str, limit: int = 5):
        # FTS5 全文检索
        cursor = self.conn.execute(
            "SELECT * FROM memories_fts WHERE memories_fts MATCH ? LIMIT ?",
            (query, limit)
        )
        return cursor.fetchall()
```

### 8.2 Markdown 文件存储

- `~/.hermes/skills/` — 技能文件（`.md`）
- `~/.hermes/memory/` — 长期记忆（`MEMORY.md`、`USER.md`）

---

## 九、安全模型

### 9.1 工具可见性与执行分离

```
用户可见工具集（prompt 中列出） ⊆ 实际可执行工具集
```

模型只能调用 prompt 中**描述过的工具**，实际执行由服务端控制，防止 prompt 注入绕过。

### 9.2 审批回调（Approval Callback）

```python
# 危险操作必须经过审批
if tool_name in DANGEROUS_TOOLS and not self.approval_callback():
    raise PermissionError(f"Tool {tool_name} requires approval")

# 审批回调示例
def my_approval_callback(tool_name: str, args: dict) -> bool:
    print(f"Approve {tool_name}?")
    return input("y/N: ").lower() == "y"
```

### 9.3 秘密自动脱敏

```yaml
security:
  redact_secrets: true   # 自动替换 API Key、密码等为 [REDACTED]
```

---

## 十、可扩展性体系

### 10.1 MCP 扩展（Model Context Protocol）

```python
# MCP 服务器作为外部工具集接入
self.mcp_servers = [
    {"name": "git", "url": "https://mcp-git-server.com"},
    {"name": "filesystem", "url": "https://mcp-fs-server.com"},
]
# 每个 MCP server 有自己的工具白名单
```

### 10.2 自定义 Toolset 插件

```python
# 注册自定义 toolset
self._tools["my_custom_toolset"] = CustomToolset(
    name="my_custom",
    tools=[my_tool1, my_tool2],
    description="自定义工具集"
)
```

### 10.3 Profiles 多实例

```bash
# 使用不同配置启动多个实例
hermes --profile work      # ~/.hermes/profiles/work/config.yaml
hermes --profile personal  # ~/.hermes/profiles/personal/config.yaml
```

---

## 十一、设计哲学总结

| 原则 | 体现 |
|------|------|
| **单进程内聚** | 核心逻辑在 `run_agent.py` 单文件中，工具调用链路短 |
| **OpenAI 兼容格式** | 消息格式完全兼容 OpenAI API，降低接入成本 |
| **回调驱动扩展** | 四大回调（clarify/approval/sudo/progress）支持任意扩展 |
| **渐进式上下文加载** | Skills/SOUL/AGENTS 分层按需加载，节省 token |
| **Fail-Closed** | 危险操作默认禁止，超时自动拒绝 |
| **学习闭环内置** | GEPA 引擎直接嵌入 Agent Loop，不是外部脚本 |

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-learning-loop]] — GEPA 引擎详解
- [[hermes-agent-memory-architecture]] — 四层记忆架构
- [[hermes-agent-skills-system]] — 技能系统机制
- [[hermes-agent-best-practices]] — 最佳实践与工程配置
