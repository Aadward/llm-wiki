---
title: OpenCode
summary: 开源 AI 编程 Agent，GitHub 159k Stars，多模型支持，CLI/TUI/Server/Web 四种运行形态
tags: [agent, productivity, comparison]
created: 2026-05-13
updated: 2026-05-13
type: entity
sources: [raw/articles/opencode-research-2026-05.md]
confidence: high
---

# OpenCode

## 概述

**OpenCode** 是由 [Anomaly Co.](https://anomaly.co) 开发的开源 AI 编程 Agent（GitHub: [anomalyco/opencode](https://github.com/anomalyco/opencode)），定位为"终端原生 AI 编程工具"，被称为"开源版 Claude Code"。[^1]

## 关键事实

| 属性 | 数据 |
|------|------|
| GitHub Stars | **159k**（2026-05-13 实时） |
| Forks | 18.6k |
| Commits | 12,771+ |
| Contributors | 850+ |
| 月活开发者 | 6.5M |
| 许可证 | MIT |
| 主要语言 | TypeScript |

> Stars 增长轨迹（多源交叉验证）：2026-01 初 50k → 2026-01 中 64k → 2026-02 中 103k → 2026-03 初 95k+ → 2026-05 中 159k[^2][^3][^4][^5]

## 四种运行形态

OpenCode 支持多种接入方式：[^6]

| 形态 | 命令 | 用途 |
|------|------|------|
| **TUI** | `opencode` | 交互式终端界面（默认） |
| **CLI** | `opencode run 'prompt'` | 非交互脚本自动化 |
| **Server** | `opencode serve --port 4096` | Headless HTTP API 服务 |
| **Web** | `opencode web` | 浏览器 IDE 界面 |
| **Desktop** | 客户端下载 | Beta 桌面应用 |
| **IDE** | VS Code 插件 | 编辑器内集成 |

## 核心架构

```
OpenCode Server（核心大脑）
  ├── LLM 通信 & 上下文管理
  ├── 文件系统操作
  ├── 工具调用 & 权限控制
  └── OpenAPI 3.1 端点

OpenCode Client（TUI / CLI / Web / IDE）
  └── 通过 HTTP/WebSocket 与 Server 通信
```

服务器模式公开完整 OpenAPI 规范（`http://host:port/doc`），可用于 SDK 生成。[^7]

## 安装方式

全部安装方式均经官网和文档核验：[^8][^9]

```bash
# 官方一键脚本（推荐）
curl -fsSL https://opencode.ai/install | bash

# npm
npm i -g opencode-ai

# Homebrew (macOS/Linux)
brew install anomalyco/tap/opencode

# Windows Scoop
scoop install opencode

# Windows Chocolatey
choco install opencode

# Arch Linux
paru -S opencode-bin
```

验证安装：`opencode --version`

## 多模型支持

OpenCode 不绑定模型，支持 75+ LLM Provider：[^10]

- **云端**：OpenAI GPT、Anthropic Claude、Google Gemini、DeepSeek 等
- **本地**：Ollama（本地模型）
- **免费内置**：Big PickLe、GLM-4 等开箱即用模型（无需 API Key）
- **配置方式**：`opencode auth login` 或设置 `OPENROUTER_API_KEY` 等环境变量

## Plan / Build 双 Agent

OpenCode 内置两类 Agent，通过 `Tab` 键切换：[^11]

| Agent | 职责 | 默认权限 |
|-------|------|---------|
| **Build Agent** | 代码实现、重构、文件读写 | 全部工具权限（edit/bash/read/write） |
| **Plan Agent** | 代码库分析、架构规划、安全探索 | 受限 |

## Skills 系统（可扩展工作流）

Skills 是 OpenCode 的工作流复用机制。自动扫描以下路径：[^12]

| 类型 | 路径 | 适用范围 |
|------|------|---------|
| 项目本地 | `.opencode/skills/<name>/SKILL.md` | 仅当前项目 |
| 全局 | `~/.config/opencode/skills/<name>/SKILL.md` | 所有项目 |
| Claude 兼容（项目） | `.claude/skills/<name>/SKILL.md` | 仅当前项目 |
| Claude 兼容（全局） | `~/.claude/skills/<name>/SKILL.md` | 所有项目 |

**SKILL.md 结构**：
```markdown
---
name: production-code-review
description: 生产环境代码审查工作流
---

# 技能目标
...

## 执行步骤
1. ...
```

## MCP 服务器集成

OpenCode 支持 Model Context Protocol，可连接外部工具（文件系统、Git、数据库等）：[^13]

```bash
# 在 opencode.json 中配置
opencode --mcp-server "npx --yes @modelcontextprotocol/server-filesystem"
```

## 服务器部署参数

经 [opencodeguide.com](https://opencodeguide.com/zh/docs/develop/server) 第三方独立文档核验：[^14]

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--port` | `4096` | 监听端口 |
| `--hostname` | `127.0.0.1` | 监听主机名 |
| `--mdns` | `false` | mDNS 自动发现 |
| `--cors` | `[]` | 允许的浏览器源 |

身份验证：通过 `OPENCODE_SERVER_PASSWORD` 设置 HTTP Basic Auth。

## 与 OpenWork 的关系

**OpenWork** 是基于 OpenCode 的独立开源桌面 GUI 应用（[GitHub](https://github.com/opencode-ai/openwork)，2.5k+ Stars），定位为 Claude Cowork 的开源替代品：[^15]

- **Host 模式**：本地运行 opencode
- **Client 模式**：通过 URL 连接远程 opencode serve
- **实时 SSE 流**：订阅执行进度
- **权限审批 UI**：Agent 敏感操作需人工审批
- **模板系统**：保存/复用工作流

## 与 Claude Code 对比

| 维度 | OpenCode | Claude Code |
|------|----------|-------------|
| 开源 | ✅ MIT | ❌ 专有 |
| 模型灵活性 | ✅ 75+ Provider | ❌ 主要 Claude |
| 定价 | Pay-as-you-go | 订阅制 |
| 自动化能力 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Skills 系统 | ✅ | ❌ |
| MCP 支持 | ✅ | ❌ |
| 生态 | CLI + Desktop + IDE | CLI + IDE |

## 相关页面

- [[openclaw-hermes-decision-tree]] — OpenClaw vs Hermes 选型决策
- [[openclaw-hermes-comparison]] — 深度对比分析
- [[persistent-agent-patterns]] — 持久 Agent 核心模式
- [[hermes-agent-best-practices]] — Hermes Agent 最佳实践

---

[^1]: [GitHub anomalyco/opencode](https://github.com/anomalyco/opencode)（2026-05-13 实时数据）
[^2]: [知乎 - opencode又爆火了](https://www.zhihu.com/question/1992364028186615862/answer/1994292843397797221)（2026-01-12，6.4万）
[^3]: [头条 - 每天一个优秀的github项目](https://www.toutiao.com/article/7605827397505516075/)（2026-02-12，10.3万）
[^4]: [CSDN - OpenCode-开源AI编程神器完全指南](https://blog.csdn.net/namelessmyth/article/details/158040706)（2026-03-07，9.5万+）
[^5]: [opencode.ai 官网](https://opencode.ai/)（2026-05-13，显示 150k）
[^6]: [opencode.ai/docs - Usage](https://opencode.ai/docs)
[^7]: [opencodeguide.com - OpenCode Server](https://opencodeguide.com/zh/docs/develop/server)
[^8]: [opencode.ai 官网 Install 选项卡](https://opencode.ai/)（带复制按钮的直接展示）
[^9]: [opencode.ai/docs - Install](https://opencode.ai/docs)
[^10]: [CSDN - OpenCode 详细攻略](https://www.cnblogs.com/tech-shrimp/articles/19837023)
[^11]: [GitHub opencode-practise - OpenCode 深度解析](https://github.com/ForceInjection/opencode-practise/blob/main/opencode_deep_dive.md)
[^12]: [runoob.com - OpenCode Skills](https://www.runoob.com/opencode/opencode-skills.html)
[^13]: [opencode.ai/docs - MCP servers](https://opencode.ai/docs)
[^14]: [opencodeguide.com - OpenCode Server](https://opencodeguide.com/zh/docs/develop/server)（第三方独立文档）
[^15]: [腾讯新闻 - Open Cowork来了](https://new.qq.com/rain/a/20260301A02YQC00)（2026-03-01）
