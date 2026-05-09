---
source_url: https://cloud.tencent.com/developer/article/2647461
ingested: 2026-05-09
sha256: 228aecd2f514abae7f897396afdd1893ae880558b3bb677e61a2b2c5f4cbb46e
---

# OpenClaw v2026.3.28 核心更新

## 发布信息
- **版本**: v2026.3.28
- **发布日期**: 2026-03-28 至 2026-03-30（文章发布）
- **主题**: requireApproval 安全阀 + xAI/Grok 整合 + MiniMax image-01 + ACP 平台绑定

## 核心更新

### 1. requireApproval —— 自主 Agent 的"安全阀"（最重要）

**机制**：在 `before_tool_call` 钩子中新增异步 `requireApproval` 能力。

当 Agent 即将执行高危操作（删除文件、发送消息、调用外部 API）时：
- 系统**暂停执行**
- 通过多渠道向用户弹出确认请求：
  - Telegram 按钮
  - Discord 互动界面
  - /approve 命令（任意频道）
  - Exec 审批浮层（控制台 UI）

**设计意义**：
- OpenClaw 走向"生产可用"的关键一步
- Cisco 安全团队记录过第三方 Skill 未授权数据外泄事件
- 引入意味着 OpenClaw 承认：**全自动不等于安全，受控自治才是正道**

**背景洞察**：功能出现时机恰好在中国政府限制国企使用 OpenClaw 之后，可能有监管压力背景。

### 2. xAI / Grok 深度整合 —— 搜索能力原生化

- Grok 搜索能力深度集成
- 搜索从外挂功能变成基础设施
- 未来 Agent 竞争力 = "能获取多实时信息来做任务"

### 3. MiniMax 图像生成 —— 多模态能力扩张

- 新增 `image-01` 模型支持
- 同时支持：text-to-image、image-to-text、多模态对话
- 精简 MiniMax 模型目录，仅保留 M2.7，移除旧版本
- 版本策略体现"不堆砌，而是整合"

### 4. ACP 多平台"当前对话绑定"

- `/acp spawn codex --bind here` 可将 Discord、BlueBubbles、iMessage 当前聊天直接变成 Codex 工作区
- 无需创建子线程
- "Agent 即界面"理念体现：对话本身就是工作台

## 版本演进背景

| 时间 | 名称 | 事件 |
|------|------|------|
| 2025.11 | Clawdbot | 首次发布 |
| 2026.1.27 | Moltbot | Anthropic 商标投诉，被迫更名 |
| 2026.1.30 | OpenClaw | "Moltbot 念起来实在拗口" |
| 2026.3.28 | OpenClaw v2026.3.28 | requireApproval 安全机制引入 |

截至 v2026.3.28，OpenClaw GitHub Stars 达 339k，成为开源历史上增长最快的项目之一。

## 安全演进路径

- v2026.2.23：安全强化（HTTP 安全头、SSRF 策略调整、配置快照脱敏）
- v2026.3.11：不可变版本（WebSocket 源验证、插件隔离、session 沙盒）
- v2026.3.28：**requireApproval** —— 从被动安全到主动可控

## 与 Hermes 对比

| 维度 | OpenClaw requireApproval | Hermes approval_callback |
|------|-------------------------|-------------------------|
| 触发机制 | 异步 before_tool_call 钩子 | 同步回调 |
| 审批方式 | 多渠道（TG/DC/控制台/命令） | 超时自动拒绝（默认 60s） |
| 哲学 | fail-closed（默认暂停） | default-allow + callback |
| 集成深度 | 核心内置 | 回调机制 |

**注意**：v2026.3.28 的 requireApproval 是 OpenClaw 补齐 Hermes 审批机制的重要节点，两者现在能力相当。
