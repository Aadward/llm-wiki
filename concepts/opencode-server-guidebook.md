---
title: OpenCode 服务器部署与生产工作流 Guidebook
summary: 在服务器上部署 OpenCode CLI、配置守护进程、构建 Long-Running Agent 生产工作流的实操手册
tags: [agent, workflow, infra, productivity]
created: 2026-05-13
updated: 2026-05-13
type: concept
sources: [raw/articles/opencode-research-2026-05.md]
confidence: high
---

# OpenCode 服务器部署与生产工作流 Guidebook

> 本文面向在服务器上部署 OpenCode 并构建生产级 Agent 工作流的工程师。
> 前置知识：基本 Linux 命令行、systemd、了解 AI Agent 概念。
> 参考：[[opencode|OpenCode 实体页]]

---

## 场景分类

| 场景 | 推荐形态 | 说明 |
|------|---------|------|
| 个人开发辅助 | TUI 交互 | 本地启动，直接对话 |
| 自动化脚本/CI | CLI 非交互 | `opencode run '...'` |
| 服务器常驻服务 | Server 模式 | systemd 守护，远程连接 |
| 团队共享 | Server + OpenWork | GUI 界面 + 权限审批 |
| 浏览器远程访问 | Web 模式 | 任意设备，无客户端 |

---

## 一、安装

### 1.1 官方一键脚本（推荐）

```bash
curl -fsSL https://opencode.ai/install | bash
```

安装后**重启终端**或重新加载 PATH，然后验证：

```bash
opencode --version
```

### 1.2 各平台备选安装

```bash
# npm（需要 Node.js >= 18）
npm i -g opencode-ai

# Homebrew (macOS/Linux)
brew install anomalyco/tap/opencode

# Windows Scoop
scoop install opencode

# Arch Linux
paru -S opencode-bin
```

### 1.3 前置要求

- Linux/macOS/WSL（Windows 推荐 WSL）
- 现代终端模拟器（WezTerm/Alacritty/Ghostty/Kitty）
- 至少一个 LLM Provider 的 API Key

---

## 二、快速上手

### 2.1 配置模型 Provider

```bash
# 方式1：交互式登录（推荐）
opencode auth login

# 方式2：直接设置环境变量
export OPENROUTER_API_KEY="your-key"
export ANTHROPIC_API_KEY="your-key"
```

### 2.2 验证安装（烟雾测试）

```bash
opencode run 'Respond with exactly: OPENCODE_SMOKE_OK'
```

### 2.3 初始化项目（可选）

```bash
cd /path/to/your/project
opencode
# 在 TUI 中输入 /init 或 Ctrl+x i 创建 AGENTS.md
```

---

## 三、TUI 交互模式

```bash
# 基本启动（当前目录）
opencode

# 指定项目目录
opencode /path/to/project

# 继续上次会话
opencode --continue
# 或
opencode -c

# 继续指定会话
opencode --session ses_abc123
# 或
opencode -s ses_abc123
```

### TUI 快捷键

| 快捷键 | 功能 |
|--------|------|
| `Enter` | 发送消息（必要时按两次） |
| `Tab` | 切换 Agent（Plan ↔ Build） |
| `Ctrl+x h` | 帮助 |
| `Ctrl+x m` | 切换模型 |
| `Ctrl+x n` | 新会话 |
| `Ctrl+x l` | 列出历史会话 |
| `Ctrl+x e` | 打开外部编辑器 |
| `Ctrl+x t` | 切换主题 |
| `Ctrl+C` | **退出**（⚠️ 不要输入 `/exit`） |

> **重要**：`/exit` 不是有效命令，会打开 agent 选择器。正确退出用 `Ctrl+C`。

---

## 四、CLI 非交互模式

适合 CI/CD、脚本自动化：

```bash
# 基本用法
opencode run 'Add retry logic to API calls and update tests'

# 指定模型
opencode run 'Refactor auth module' --model openrouter/anthropic/claude-sonnet-4

# 附加文件
opencode run 'Review this config for security issues' \
  -f config.yaml -f .env.example

# 显示思考过程
opencode run 'Debug why tests fail in CI' --thinking

# 指定工作目录
opencode run 'Implement feature X' --workdir /path/to/project
```

---

## 五、服务器常驻模式（核心）

### 5.1 手动启动

```bash
# 基本启动（仅本地访问）
opencode serve

# 局域网/远程访问
opencode serve --port 4096 --hostname 0.0.0.0

# 允许特定浏览器源（可多次传递）
opencode serve --port 4096 --hostname 0.0.0.0 \
  --cors http://localhost:5173 \
  --cors https://app.example.com
```

### 5.2 身份验证

```bash
# 设置 HTTP Basic Auth 密码
OPENCODE_SERVER_PASSWORD=your_password opencode serve

# 自定义用户名（可选）
OPENCODE_SERVER_USERNAME=admin \
OPENCODE_SERVER_PASSWORD=your_password \
opencode serve
```

### 5.3 参数参考

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--port` | `4096` | 监听端口 |
| `--hostname` | `127.0.0.1` | 监听地址 |
| `--mdns` | `false` | mDNS 发现（局域网自动广播） |
| `--mdns-domain` | `opencode.local` | mDNS 自定义域名 |
| `--cors` | `[]` | 允许的浏览器源 |

### 5.4 连接远程服务器

```bash
# 连接远程 opencode serve
opencode run "task description" \
  --attach http://server-ip:4096

# 指定会话
opencode run "task" \
  --attach http://server-ip:4096 \
  --session my-session
```

### 5.5 查看 OpenAPI 文档

服务器运行后访问：`http://host:port/doc`（如 `http://localhost:4096/doc`）

---

## 六、systemd 守护进程配置（生产环境推荐）

### 6.1 创建服务文件

```bash
sudo tee /etc/systemd/system/opencode-agent.service << 'EOF'
[Unit]
Description=OpenCode AI Agent Server
After=network.target

[Service]
Type=simple
User=你的用户名
WorkingDirectory=/home/你的用户名
ExecStart=/root/.local/bin/opencode serve --port 4096 --hostname 0.0.0.0
Restart=always
RestartSec=10

# 环境变量（API Key）
Environment="OPENROUTER_API_KEY=你的密钥"
Environment="ANTHROPIC_API_KEY=你的密钥"
Environment="OPENCODE_SERVER_PASSWORD=你的服务密码"

# 日志
StandardOutput=append:/var/log/opencode-agent.log
StandardError=append:/var/log/opencode-agent.log

[Install]
WantedBy=multi-user.target
EOF
```

> 查找 opencode 二进制路径：`which opencode` 或 `ls ~/.local/bin/opencode`

### 6.2 启用并启动

```bash
sudo systemctl daemon-reload
sudo systemctl enable opencode-agent   # 开机自启
sudo systemctl start opencode-agent     # 立即启动
sudo systemctl status opencode-agent  # 确认运行
```

### 6.3 常用管理命令

```bash
systemctl status opencode-agent    # 查看状态
systemctl restart opencode-agent  # 重启
systemctl stop opencode-agent     # 停止
journalctl -u opencode-agent -f   # 实时日志
```

---

## 七、Skills 工作流复用

### 7.1 创建项目级 Skill

项目内：`/path/to/project/.opencode/skills/production-code-review/SKILL.md`

```markdown
---
name: production-code-review
description: 生产环境代码审查工作流
---

# 生产环境代码审查

## 触发条件
- PR 合入 main 分支前
- Release 前必须执行

## 执行步骤

1. **运行基础检查**
   ```bash
   npm run lint
   npm run type-check
   ```

2. **运行测试套件**
   ```bash
   npm test -- --coverage
   ```

3. **安全扫描**
   ```bash
   npm audit
   ```

4. **输出审查报告**
   - 检查项清单
   - 发现的问题
   - 修复建议
```

### 7.2 创建全局复用 Skill

路径：`~/.config/opencode/skills/my-org-deploy/SKILL.md`

### 7.3 调用 Skill

```bash
# 在项目内直接引用
opencode run "执行生产代码审查" --skill production-code-review
```

---

## 八、MCP 服务器集成

### 8.1 在 opencode.json 中配置

```bash
# 初始化配置（如尚无）
opencode --config-init

# 或手动编辑 ~/.config/opencode/opencode.json
```

添加 MCP 服务器：

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["--yes", "@modelcontextprotocol/server-filesystem", "/path/to/allowed"]
    },
    "git": {
      "command": "npx",
      "args": ["--yes", "@modelcontextprotocol/server-github"]
    }
  }
}
```

### 8.2 常用 MCP 服务器

- `@modelcontextprotocol/server-filesystem` — 文件读写
- `@modelcontextprotocol/server-github` — Git 操作
- `@modelcontextprotocol/server-sqlite` — 数据库查询
- `@modelcontextprotocol/server-brave-search` — 网页搜索

---

## 九、与 Hermes Agent 集成

OpenCode 可作为 Hermes Agent 的**子编码引擎**，构建多层 Agent 架构：

### 架构图

```
Hermes Agent（调度层）
  │
  ├── 分解任务
  ├── 调度决策
  │
  ├── opencode serve (服务器层)
  │     ├── Build Agent — 代码实现
  │     └── Plan Agent — 架构分析
  │
  └── 汇总报告 → 用户
```

### 委托模式

```bash
# Hermes 通过 terminal() 调用 opencode run
opencode run '实现用户认证模块，编写单元测试' \
  --model openrouter/anthropic/claude-sonnet-4 \
  --workdir /path/to/project
```

### 后台长任务模式

```bash
# 启动命名会话
opencode --session daily-build-$(date +%Y%m%d) \
  --workdir /path/to/project \
  --model openrouter/anthropic/claude-sonnet-4

# 后续通过 session ID 恢复
opencode -s daily-build-20260513 \
  --attach http://localhost:4096
```

---

## 十、CI/CD 集成示例

### 10.1 GitHub Actions

```yaml
name: OpenCode Review
on:
  pull_request:
    branches: [main]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup OpenCode
        run: |
          curl -fsSL https://opencode.ai/install | bash
          echo "OPENROUTER_API_KEY=${{ secrets.OPENROUTER_API_KEY }}" >> $GITHUB_ENV

      - name: Run Code Review
        run: |
          opencode run "Review PR changes for security issues, test coverage gaps, and performance problems. Output a concise report."
          --model openrouter/anthropic/claude-sonnet-4
```

### 10.2 自动化流水线触发

```
代码 push → GitHub Webhook → Hermes Agent →
  → 启动 opencode serve (per-task) →
  → opencode run "Review PR #42" →
  → 评论 PR / 发送通知
```

---

## 十一、常见问题

### Q: 提示 "command not found"

```bash
# 关闭终端重新打开，或手动加载 PATH
source ~/.bashrc

# 确认安装位置
which opencode
ls ~/.local/bin/opencode
```

### Q: API Key 配置在哪？

```bash
# 环境变量（推荐生产）
echo 'export OPENROUTER_API_KEY="sk-..."' >> ~/.bashrc
source ~/.bashrc

# 交互式（适合本机开发）
opencode auth login
```

### Q: `/exit` 无法退出

正确退出 TUI 的方式：
- **方式1**：`Ctrl+C`（发送 SIGINT）
- **方式2**：`process(action="kill")`（通过 Hermes 调用时）

### Q: 服务器模式安全吗？

- **局域网使用**：默认仅 `127.0.0.1:4096`，安全
- **暴露到公网**：必须设置 `OPENCODE_SERVER_PASSWORD` + 防火墙限制
- **推荐**：配合 VPN 或 SSH 隧道，不要直接暴露到公网

### Q: 如何查看服务器状态？

```bash
curl http://localhost:4096/global/health
# 返回: {"healthy":true,"version":"x.x.x"}
```

---

## 十二、快速检查清单

- [ ] 安装：`curl -fsSL https://opencode.ai/install | bash`
- [ ] 验证：`opencode --version`
- [ ] 配置 API Key（`opencode auth login` 或环境变量）
- [ ] 烟雾测试：`opencode run 'Respond with: OK'`
- [ ] 服务器部署：`systemctl start opencode-agent`
- [ ] 验证服务：`curl http://localhost:4096/global/health`
- [ ] 创建第一个 Skill：`.opencode/skills/<name>/SKILL.md`
- [ ] 连接远程：`opencode run --attach http://server:4096 "task"`

---

## 相关页面

- [[opencode|OpenCode 实体页]] — 项目概览、Stars 增长、架构说明
- [[openclaw-hermes-decision-tree|选型决策树]] — OpenCode vs OpenClaw vs Hermes
- [[persistent-agent-patterns|持久 Agent 核心模式]] — Heartbeat 驱动、自愈机制
- [[scheduled-automation|定时自动化]] — Cron 设计、Morning Briefing
- [[hermes-agent-best-practices|Hermes 最佳实践]] — 多 Agent 协作、凭证管理
