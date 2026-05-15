---
title: NemoClaw 最佳实践
created: 2026-05-15
updated: 2026-05-15
type: concept
tags: [agent, security, workflow]
sources: [raw/articles/nvidia-nemoclaw-2026.md]
confidence: high
---

# NemoClaw 最佳实践

## 安全边界管理

### OpenShell 生命周期边界

> ⚠️ **最容易犯的错误**：直接用 `openshell` 命令管理 NemoClaw 创建的沙箱。

| 操作 | 正确方式 | 错误方式 |
|---|---|---|
| 创建/重建沙箱 | `nemoclaw onboard` | `openshell sandbox create` |
| 更新 Gateway | `nemoclaw onboard`（重建）| `openshell self-update` |
| 启动 Gateway | `nemoclaw onboard` | `openshell gateway start` |
| 运行时策略变更 | `openshell policy update`（热重载）| — |

### 凭证管理

- Provider 凭证**只存在宿主机**，沙箱只有 placeholder token
- 凭证错误时用 `nemoclaw credentials reset <PROVIDER>` 清除
- 不要在沙箱内明文存储任何 API key
- 重建沙箱时如果报错 `Missing credential`，重新运行 `nemoclaw onboard` 刷新 session metadata

## 升级与回滚

### 安全升级流程

```bash
# 1. 升级前打快照（推荐）
nemoclaw <sandbox-name> snapshot create --name pre-upgrade

# 2. 升级 CLI
nemoclaw update

# 3. 检查沙箱状态
nemoclaw upgrade-sandboxes --check

# 4. 执行升级
nemoclaw upgrade-sandboxes

# 5. 出问题回滚
nemoclaw <sandbox-name> snapshot restore pre-upgrade
```

### rebuild 的边界

- ✅ **保留**：workspace 目录（`/sandbox/.openclaw/workspace/`）、注册的政策、credentials
- ❌ **不保留**：apt/pip 安装的包、非 workspace 路径的文件、进程内存状态
- 如需保留 runtime 定制，在 rebuild 前将修改固化到 Dockerfile 并用 `nemoclaw onboard --from` 重建

## 策略管理

### 策略层级选择

| 层级 | 适用场景 |
|---|---|
| `Restricted` | 最高安全要求、无第三方网络依赖 |
| `Balanced`（默认） | 通用开发工具（npm、pypi、HuggingFace）、无消息平台 |
| `Open` | 需要 Slack/Discord/Telegram/Jira/Outlook 的完整集成 |

### 动态策略更新（热重载）

```bash
# 运行时添加端点（无需重启）
openshell policy update <sandbox-name> \
  --add-endpoint api.example.com:443:read-only:rest:enforce

# 替换完整策略
openshell policy set --policy <policy-file> <sandbox-name>
```

### 社区 Preset（awesome-nemoclaw）

在 `nemoclaw-blueprint/policies/presets/` 目录外，还有 VoltAgent/awesome-nemoclaw 社区维护的 preset：

覆盖：GitLab、Notion、Linear、Confluence、Teams、Zendesk、Sentry、Stripe、Cloudflare、Google Workspace、AWS、GCP、Vercel、Supabase、Neon、Algolia、Airtable、HubSpot 等。

## 内存与性能

- **最低要求**：4 vCPU、8 GB RAM、20 GB 磁盘
- **推荐**：16 GB RAM、40 GB 磁盘
- 镜像 2.4 GB + Docker/k3s/Gateway 并发运行，< 8 GB RAM 机器可能触发 OOM
- 解决方案：配置至少 8 GB swap（以性能为代价）

## 多沙箱端口隔离

每个沙箱需要独立 dashboard 端口。`nemoclaw onboard` 自动扫描 `18789~18799` 找空闲端口。

```bash
# 手动指定端口
nemoclaw onboard --control-ui-port 19000
```

## Landlock 验证（生产必检）

```bash
ls /sys/kernel/security/landlock
```

生产部署需要 kernel 5.13+ 且 Landlock 可用。e2e 检查脚本：
```
test/e2e/e2e-cloud-experimental/checks/04-landlock-readonly.sh
```

## 日常运维命令速查

```bash
# 连接沙箱
nemoclaw my-assistant connect

# 健康检查
nemoclaw my-assistant status
nemoclaw status   # 包含 host 级服务状态

# 日志
nemoclaw my-assistant logs
nemoclaw my-assistant logs --follow

# 诊断收集
nemoclaw debug --sandbox my-assistant --output debug.tar.gz
nemoclaw debug --quick --sandbox my-assistant

# 恢复 gateway（sandbox 存活但 gateway 挂掉）
nemoclaw <sandbox-name> recover

# 运行时切换推理模型
nemoclaw inference set --model <model> --provider <provider>

# 卸载
nemoclaw uninstall --yes --keep-openshell
```

## 反模式

1. **在沙箱内安装系统包**（apt/pip）— rebuild 不保留，应固化到 Dockerfile
2. **直接调用 openshell 命令** — 绕过 nemoclaw 导致状态不一致
3. **忽略 Landlock 验证** — kernel 不支持时退化为仅 DAC 保护
4. **内存不足不配 swap** — 沙箱构建时 OOM kill
5. **升级前不打快照** — 出问题无法回滚

## 相关页面

- [[nemoclaw|NemoClaw 实体页]]
- [[nemoclaw-architecture|NemoClaw 架构深度解析]]
- [[openclaw|OpenClaw]]
- [[security-model-comparison|安全模型对比]]
- [[persistent-agent-patterns|持久 Agent 模式]]
