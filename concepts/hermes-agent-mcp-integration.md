---
title: Hermes Agent MCP 集成最佳实践
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, mcp, integration, security, whitelist]
sources: [web/cloud.tencent.com/developer/article/2662531]
confidence: high
---

# Hermes Agent MCP 集成最佳实践

## 概述

MCP（Model Context Protocol）是 Agent 世界的"USB 标准"——Hermes Agent 通过 MCP 连接外部系统，像调用普通工具一样调用 MCP Server 暴露的能力。

**核心理念**：最佳实践不是"连接一切"，而是：**只连接正确的内容，并且只暴露最小但够用的能力范围**。

---

## 一、什么时候该用 / 不该用 MCP

### 适合用 MCP 的情况

- 已存在 MCP 形式的工具（Server），你不想再构建原生 Hermes 工具
- 希望 Hermes 通过**干净的 RPC 层**与本地或远程系统交互（而不是把逻辑塞进 Agent 侧）
- 需要"按 Server 维度"的**细粒度暴露控制**（哪些工具可见、哪些功能禁用）
- 要接内部 API、数据库、公司系统，但**不想改 Hermes 核心**

### 不适合用 MCP 的情况

- Hermes 内置工具已能很好完成任务
- Server 暴露了大量危险工具，而你暂时没有准备做过滤/审计
- 只需要一个非常狭窄的集成（原生工具可能更简单、更安全）

---

## 二、思维模型：MCP 作为"适配层"

```
Hermes Agent（推理/计划/选择工具/推进任务）
        ↓ 调用工具
MCP Server（提供外部工具：文件、GitHub、数据库、内部系统）
        ↓
实际系统（文件系统、GitHub API、公司数据库）
```

**关键原则**：
- Hermes 负责智能决策
- MCP Server 负责暴露工具
- **你负责控制每个 Server 的暴露面**（最关键的点）

**好的 MCP 接入 = 连接正确的系统 + 严格限制暴露面 + 可观测（可验证、可排错）**

---

## 三、安装 MCP 支持

### 自动安装

标准安装脚本（`install.sh`）通常已包含 MCP 支持。

### 手动补装

```bash
cd ~/.hermes
npm install -g @anthropic-ai/mcp-server  # 或对应 MCP server 包
```

**补充建议**：
- 确保本机有 Node.js，且 `npx` 可用
- 很多场景 `@modelcontextprotocol/server-filesystem` 是一个不错的默认选择

---

## 四、白名单模式：工具筛选

### 4.1 为什么必须做工具筛选

过滤要从**第一天**就做。尤其是以下敏感场景：
- GitHub（代码仓库、生产环境）
- 支付系统
- 客户数据
- 内部系统

### 4.2 单目录文件系统（最安全起点）

从单一、低风险、可控范围的小 Server 起步。最经典的是"只给一个项目目录的文件系统访问"：

```yaml
# config.yaml
mcp_servers:
  project_fs:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/my-project"]
```

**关键点**：
- 给**具体路径**，不要给整个 `/` 或 `$HOME`
- 限制在项目目录内，泄漏风险可控

### 4.3 GitHub 集成（白名单工具）

```yaml
mcp_servers:
  github:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    tools:
      include:
        - read_file
        - write_file
        - git_status
        - git_log
        # 不暴露: git_push, git_force_push, issue_create, issue_close
```

### 4.4 多 Server 场景

```yaml
mcp_servers:
  # 只读文件系统
  readonly_fs:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-filesystem", "/data/docs"]
    tools:
      include:
        - read_file
        - list_directory

  # 受限 GitHub
  github_read:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    tools:
      include:
        - read_file
        - git_status
```

### 4.5 工具过滤配置说明

| 字段 | 含义 |
|------|------|
| `tools.include` | 白名单：只有这些工具可用 |
| `tools.exclude` | 黑名单：禁用这些工具（危险工具） |
| 都不设置 | 所有工具可用（**不推荐**） |

**推荐做法**：优先使用 `tools.include` 白名单模式，明确列出每一种可用工具。

---

## 五、安全隔离架构

### 5.1 外部集成最小可用架构

```
Messaging Gateway（入口身份 + 会话管理）
        ↓
Hermes 内置 Toolsets（网页/终端等通用能力）
        ↓
MCP Server（白名单 Git + 单目录文件系统）
        ↓
危险命令审批 + 容器类 Terminal Backend
```

### 5.2 分层防御

|| 层级 | 控制机制 |
|------|------|---------|
| 入口 | Messaging Gateway | 身份验证 + 会话隔离 |
| 工具 | 内置 Toolsets | 按场景启/禁用 |
| MCP | 白名单模式 | Server 维度工具过滤 |
| 执行 | 容器 Terminal Backend | 命令隔离 |
| 审批 | approval_callback | 危险操作人工确认 |

### 5.3 终端沙箱配置

```yaml
# 生产环境推荐用 Docker 隔离
terminal:
  backend: docker  # 每个命令在容器中执行
  container_image: "hermes-agent/sandbox:latest"
```

---

## 六、配置示例

### 6.1 完整配置模板

```yaml
# ~/.hermes/config.yaml
mcp_servers:
  # ── 文件系统（只读，单目录）──
  docs_ro:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/docs"]
    tools:
      include:
        - read_file
        - list_directory

  # ── GitHub（只读，最小工具集）──
  github_ro:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "${GITHUB_TOKEN}"
    tools:
      include:
        - read_file
        - git_status
        - git_log
        - search_code

  # ── Slack（通知，仅发消息）──
  slack_notify:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-slack"]
    env:
      SLACK_BOT_TOKEN: "${SLACK_BOT_TOKEN}"
    tools:
      include:
        - chat_postMessage
```

### 6.2 CLI 命令行配置

```bash
# 添加 MCP Server（白名单工具）
hermes mcp add git-server \
  --url https://mcp-server.example.com \
  --allowed-tools read_file,write_file,git_status

# 查看当前 MCP 工具
hermes mcp list

# 验证 MCP 连接
hermes mcp check git-server
```

---

## 七、最佳实践 Checklist

### 起步阶段
- [ ] 从单目录文件系统开始（最低风险）
- [ ] 始终使用 `tools.include` 白名单
- [ ] 不给根路径或 `$HOME` 路径访问
- [ ] 先只读，再按需扩展写权限

### 安全加固
- [ ] GitHub token 使用最小权限（read-only）
- [ ] Terminal 后端使用 Docker 容器隔离
- [ ] 配置 `approval_callback` 处理危险操作
- [ ] 定期审计 `~/.hermes/config.yaml` 中的 MCP 配置

### 生产环境
- [ ] MCP Server 日志接入统一日志系统
- [ ] 为每个 MCP Server 设置资源限制（超时、频率）
- [ ] 使用 secrets 管理 API Token，不写在配置文件中

---

## 八、常见错误与排错

| 症状 | 原因 | 解决 |
|------|------|------|
| `MCP server not found` | npx 未安装或路径不对 | `npm install -g npx` |
| `Tool not allowed` | 工具不在白名单中 | 检查 `tools.include` 配置 |
| `Permission denied` | token 权限不足 | 确认 API Token 范围 |
| `Connection timeout` | MCP Server 启动慢 | 增加 `startup_timeout` 配置 |
| `Invalid arguments` | 参数格式错误 | 检查 `args` 数组格式 |

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-source-code-architecture]] — 源码架构（工具注册机制）
- [[hermes-agent-best-practices]] — 最佳实践与工程配置
- [[security-credential-management]] — 安全与凭证管理
