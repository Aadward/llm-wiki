---
title: Hermes Agent 排错深度指南
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, troubleshooting, error-fixing, debugging, common-errors]
sources: [web/qiniushanghai.com, web/segmentfault.com/a/1190000047722909, web/blog.csdn.net/qq_15071263/article/details/160309851]
confidence: high
---

# Hermes Agent 排错深度指南

## 概述

Hermes Agent 的报错按阶段可分为四组：**安装期**、**运行期认证类**、**后端基础设施类**、**长任务执行类**。本指南覆盖 10 类常见错误与修复方案。

---

## 快速自检三步法

遇到任何问题，先运行这三条命令：

```bash
# 1. 全面健康检查（覆盖 API Key、端点可达性、Token 长度、OAuth 状态）
hermes doctor

# 2. 查看当前生效配置（确认 provider、model、base_url 字段是否存在）
hermes config show

# 3. 查看最新错误日志（所有错误自动写入，secrets 自动脱敏）
cat ~/.hermes/logs/errors.log | tail -50
```

> `hermes doctor` 覆盖 80% 以上的配置类问题，**先跑它再看后续各节**。

---

## 一、报错全景总览

| 类别 | 典型报错 | 发生阶段 |
|------|---------|---------|
| ① 命令找不到 | `hermes: command not found` | 安装后首次运行 |
| ② Python 版本不兼容 | `SyntaxError: f-string expression / requires Python >=3.10` | 安装时 |
| ③ API Key 认证失败 | `AuthenticationError: Invalid API key` | 运行时 |
| ④ 速率限制 | `RateLimitError: Too many requests` | 运行时 |
| ⑤ Docker 后端未就绪 | `Cannot connect to Docker daemon` | 切换 Docker 后端时 |
| ⑥ Docker 权限错误 | `permission denied while trying to connect to the Docker daemon` | 运行时 |
| ⑦ MCP 服务器连接失败 | `MCP server connection failed` | MCP 集成时 |
| ⑧ OAuth 令牌过期 | `Token expired or invalid v0.8 MCP OAuth 2.1` | 运行时 |
| ⑨ 上下文窗口溢出 | `context length exceeded / max_tokens` | 长任务运行中 |
| ⑩ Subagent RPC 超时 | `Subagent RPC timeout after 30s` | 并行子代理任务 |

---

## 二、安装期报错

### 2.1 hermes: command not found

**这是最常见的安装后报错**，根因是 PATH 未刷新，而非 Hermes Agent 未安装成功。

```bash
# 步骤 1：重新加载 shell 配置
source ~/.bashrc    # bash 用户
source ~/.zshrc     # zsh 用户

# 步骤 2：验证安装
hermes --version

# 如果仍然找不到，手动添加到 PATH
export PATH="$HOME/.local/bin:$PATH"
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
```

### 2.2 Python 版本不兼容

```bash
# 检查当前 Python 版本
python --version

# 需要 Python 3.10+（推荐 3.11+）

# macOS 升级
brew install python@3.12

# Ubuntu/Debian 升级
sudo apt install python3.12 python3.12-venv
```

### 2.3 pip 安装超时

```bash
# 使用国内镜像
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# 或配置全局镜像
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 2.4 网络连接问题

```bash
# 测试网络连接
ping raw.githubusercontent.com

# 如果 ping 不通，使用代理
export https_proxy=http://127.0.0.1:7890
export http_proxy=http://127.0.0.1:7890

# 手动安装
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
pip install -e .
```

---

## 三、运行期认证类报错

### 3.1 API Key 认证失败

**原因分析**：
- `.env` 文件中的 API Key 变量名与 `config.yaml` 中指定的模型提供商不匹配
- 模型名称拼写错误
- Windows 环境下，环境变量未能正确加载

```bash
# 检查配置文件
cat ~/.hermes/config.yaml | grep -A5 "model:"
cat ~/.hermes/.env | grep API

# 强制重新指定模型（推荐）
/model openai/gpt-4o

# 或使用 hermes model 命令
hermes model set openai/gpt-4o
```

### 3.2 速率限制

```bash
# 等待后重试，或设置限流
# 在 config.yaml 中添加
model:
  default: openai/gpt-4o
  rate_limit:
    max_requests_per_minute: 60
```

---

## 四、后端基础设施类报错

### 4.1 Docker 后端未就绪

```bash
# 检查 Docker 是否运行
docker ps

# 启动 Docker 服务
sudo systemctl start docker

# 添加当前用户到 docker 组
sudo usermod -aG docker $USER
newgrp docker
```

### 4.2 Docker 权限错误

```bash
# 权限问题修复
sudo chmod 666 /var/run/docker.sock

# 或创建 docker 组
sudo groupadd docker
sudo usermod -aG docker $USER
```

### 4.3 MCP 服务器连接失败

```bash
# 检查 MCP 配置
hermes mcp list

# 测试 MCP 服务器
hermes mcp test <server-name>

# 常见修复：检查 config.yaml 中的 MCP 配置
mcp:
  servers:
    - name: my-server
      command: npx -y @modelcontextprotocol/server-filesystem
      args: ["/path/to/directory"]
```

### 4.4 OAuth 令牌过期

```bash
# 重新认证
hermes auth refresh

# 或删除旧令牌重新登录
rm ~/.hermes/.auth_tokens
hermes auth login
```

---

## 五、长任务执行类报错

### 5.1 上下文窗口溢出

```bash
# 在 config.yaml 中配置更大的 max_tokens
model:
  default: openai/gpt-4o
  max_tokens: 4096

# 或切换到支持更长上下文的模型
/model openai/gpt-4o-128k
```

### 5.2 Subagent RPC 超时

```bash
# 增加超时时间
hermes config set subagent.timeout 60

# 或在 config.yaml 中
subagent:
  rpc_timeout: 60
```

---

## 六、消息网关报错

### 6.1 Telegram Gateway 无法启动

```bash
# 检查 Bot Token
hermes gateway config telegram

# 重启 Gateway
hermes gateway restart telegram

# 查看 Gateway 日志
hermes gateway logs telegram
```

### 6.2 WSL 网关掉线

```bash
# 在 WSL 中使用 systemd
sudo systemctl enable hermes-gateway
sudo systemctl start hermes-gateway

# 或使用后台运行
hermes gateway start --background
```

### 6.3 macOS PATH 丢失

```bash
# 在 ~/.zshrc 中添加
export PATH="/usr/local/bin:$PATH"

# 重载
source ~/.zshrc
```

---

## 七、诊断命令速查表

| 命令 | 作用 |
|------|------|
| `hermes doctor` | 全面健康检查 |
| `hermes config show` | 查看当前配置 |
| `hermes logs` | 查看主日志 |
| `hermes gateway logs` | 查看网关日志 |
| `hermes model list` | 列出可用模型 |
| `hermes skills list` | 列出已安装技能 |
| `hermes mcp list` | 列出 MCP 服务器 |
| `hermes doctor --verbose` | 详细诊断信息 |

---

## 八、高频踩坑提醒

1. **API Key 写到错误文件**：`.env` 文件必须放在 `~/.hermes/` 下
2. **`/model` 和 `hermes model` 命令混用**：在 TUI 中用 `/model`，在 CLI 中用 `hermes model`
3. **Ollama 上下文默认值太低**：默认 4096，远低于要求的 64k 最低值
4. **多 Profile 共用 Bot Token**：不同 Profile 使用不同 Bot Token，否则会互相冲突
5. **Windows 原生不支持**：必须使用 WSL2

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-best-practices]] — 最佳实践
- [[hermes-agent-configuration]] — 配置详解
