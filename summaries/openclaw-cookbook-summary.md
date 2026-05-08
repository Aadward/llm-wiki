---
source_url: https://github.com/hesamsheikh/awesome-openclaw-usecases
ingested: 2026-05-08
updated: 2026-05-08
sha256: <computed-from-openclaw-cookbook-34kb>
---

# OpenClaw Cookbook 摘要

> 来源：awesome-openclaw-usecases，42 个真实用例
> 最后更新：2026-05-08

## 1. 理念与核心概念

OpenClaw Agent 是**持久的、工具增强的 AI 助手**，持续运行。它们跨会话维护长期记忆，可以生成子 Agent 并行工作，将消息平台作为 UI 连接，并自主运行定时任务。

**核心原语工具包：**
- Memory（Markdown 文件，持久化）
- Sessions（`sessions_spawn` / `sessions_send`）
- Skills（ClawHub 模块）
- MCP Servers（工具扩展）
- Cron / Heartbeat（自主任务）
- AGENTS.md / SOUL.md / HEARTBEAT.md（配置文件）

## 2. 基础模式

### 模式：消息平台即 UI

将 Telegram、Discord、WhatsApp 作为界面使用。多 Agent 设置使用频道路由——不同 Agent 响应不同的 @mention。

### 模式：零摩擦记忆捕获

捕获 = 只需给机器人发消息。你告诉它越多，它记得越多。检索 = 基于语义搜索。

### 模式：STATE.yaml 协调

使用共享 YAML 文件代替编排器消息传递。更可扩展、可 git 审计。

## 3. 多 Agent 编排

**CEO 模式：** 主会话保持轻薄——只生成、路由、总结。所有执行交给子 Agent。

**专业团队：** 2-4 个 Agent，各有角色/个性/模型，可从一个 Telegram 群控制。从 2 个开始，不要从 4 个开始。

**STATE.yaml 竞态条件修复：**
- `AUTONOMOUS.md` — 仅主会话，<50 行
- `memory/tasks-log.md` — 仅追加，子 Agent 使用

**n8n 代理模式：** Agent → webhook → n8n 工作流 → 外部 API（凭证留在 n8n）。

## 4. 记忆与知识系统

- 内置 Markdown 记忆（累积性、永久性）
- 通过 memsearch 做语义搜索（Markdown 文件上的向量索引，BM25+RRF 混合）
- RAG 知识库（输入 URL → 语义 ingest → 查询）
- 第二大脑仪表板（Next.js，由 Agent 构建）

## 5. 定时自动化与 Cron

Cron 是真正的产品——主动式 Agent，无需提示即可主动呈现洞察。

**常见调度结构：**
- 每 15 分钟：任务看板检查
- 每小时：健康检查、邮件分类
- 每 6 小时：知识库同步、自检
- 每天 8AM：早间简报（天气、日历、系统状态、任务）
- 每周：安全审计

## 6. 安全与凭证管理

**头号风险：** Agent 在代码中硬编码 API 密钥。

**防御层次：**
- TruffleHog 预推送钩子（所有仓库）
- 本地优先 Git（私有 Gitea → 公开 GitHub）
- n8n 凭证隔离
- 分支保护（主分支需要 PR）
- 每日自动化安全审计
- 构建 → 测试 → 锁定周期（n8n 工作流）

## 7. 基础设施与 DevOps

Agent 拥有 SSH + kubectl + terraform + ansible 权限，可访问家庭网络。早间简报包含系统健康状态、服务状态、最近部署、告警。

## 8. 内容流水线

- YouTube 内容流水线：研究 → 脚本 → 缩略图
- 多 Agent 内容工厂：3 个 Discord 频道（研究/脚本/缩略图）
- 播客制作：嘉宾研究 → 大纲 → 节目笔记 → 社交推广
- AI 视频编辑：自然语言视频操控

## 9. 专业工作流

- **构建前创意验证：** idea-reality-mcp 在构建前扫描 GitHub/HN/npm/PyPI
- **市场调研 → 产品工厂：** Last30Days skill 从 Reddit/X 挖掘痛点
- **会议笔记 → 任务创建：** 转录 → 摘要 → Jira/Linear/Todoist 任务
- **Polymarket 自动交易：** 纸交易 + 策略模式

## 10. 反模式与陷阱

1. ❌ 硬编码密钥 → 使用 1Password CLI 或 n8n
2. ❌ STATE.yaml 竞态条件 → 拆分 AUTONOMOUS.md + tasks-log.md
3. ❌ STATE.yaml 无限制增长 → 保持 <50 行，归档已完成任务
4. ❌ 单个 Agent 包揽一切 → 使用专业 Agent
5. ❌ 跳过 n8n 锁定步骤 → 构建 → 测试 → 锁定
6. ❌ 一开始就用 4+ 个 Agent → 从 2 个开始
7. ❌ 验证输出前过度工程 → 先粘贴转录稿
8. ❌ 忘记主动式 cron → 调度 HEARTBEAT.md

## 核心 Skills 目录

| Skill | 功能 |
|-------|------|
| youtube-full | YouTube 数据 + 转录 |
| reddit-readonly | 浏览 Reddit 子版块 |
| tech-news-digest | 109+ 来源科技新闻 |
| tweetclaw | X/Twitter 自动化 |
| knowledge-base | URL RAG |
| last30days | Reddit/X 痛点挖掘 |

## OpenClaw 飞轮

```
捕获（文本） → 记住（持久记忆） → 研究（网页/RSS）
     ↓
规划（目标/任务） → 执行（子 Agent/cron） → 交付（Discord/Telegram）
     ↓
重复（Agent 随时间积累价值）
```

---

*本文档是 [[openclaw]] 的 [[openclaw-cookbook-summary]]，原始 896 行 Cookbook 位于 [[openclaw]]*
