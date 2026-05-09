---
title: OpenClaw ↔ Hermes 安全模型横向对比
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [openclaw, hermes, security, approval, credentials, mcp, fail-closed]
sources: [concepts/openclaw-2026-background-tasks-flows.md, concepts/hermes-agent-best-practices.md, concepts/hermes-agent-source-code-architecture.md, concepts/multi-agent-orchestration.md]
confidence: high
---

# OpenClaw ↔ Hermes 安全模型横向对比

## 一、两种根本不同的安全哲学

| | OpenClaw | Hermes |
|---|---|---|
| **哲学** | **预防优先，fail-closed（默认拒绝）** | **响应优先，default-allow + callback（默认允许，可干预）** |
| **设计假设** | 危险操作应该被预防 | 危险操作可以被批准 |
| **合规适配** | 企业合规（SOC2/ISO27001） | 个人灵活使用 |

---

## 二、OpenClaw 的 Fail-Closed 体系

### 2.1 2026.3.11 安全加固全景

| 维度 | 具体措施 |
|------|---------|
| **令牌管理** | trusted-proxy 拒绝混合共享令牌，令牌轮换立即断开 session |
| **节点命令** | 配对后默认禁用，需显式批准 |
| **环境隔离** | 阻止请求范围 env var 覆盖（Docker 端点、TLS 证书、包索引等） |
| **技能安装** | critical 危险代码默认失败扫描 |
| **插件安全** | 关闭隐式技能依赖安装 |

### 2.2 核心思路

**fail-closed = 默认拒绝，需要显式证明才能执行**：
- 节点命令必须配对批准
- 危险代码必须扫描通过
- 混合令牌配置直接拒绝

---

## 三、Hermes 的 Callback 体系

### 3.1 四个回调机制

| 回调 | 用途 | 文档透明度 |
|------|------|-----------|
| `clarify_callback` | 模型不确定时向用户确认 | 最低（仅参数名） |
| `approval_callback` | 危险操作需用户批准 | 少量（超时自动拒绝） |
| `sudo_callback` | 提升权限执行管理操作 | 无 |
| `progress_callback` | 报告任务进度 | 无 |

### 3.2 关键机制

```bash
# 危险命令审批超时（默认 60 秒）后自动拒绝
hermes config set approvals.timeout 120

# 跳过命令审批（信任环境）
/yolo
```

### 3.3 MCP 安全（白名单模式）

```
hermes mcp add git-server \
  --allowed-tools read_file,write_file,git_status
```

**依赖假设**：MCP server 本身是可信的，白名单只控制工具粒度。

---

## 四、两种审批模型的运作差异

| | OpenClaw | Hermes |
|---|---|---|
| **审批时机** | 配置阶段（预先定义策略） | 运行时（危险操作触发回调） |
| **超时行为** | 节点命令默认禁用 | 危险命令超时自动拒绝（60秒） |
| **绕过方式** | 需要显式配置 | `/yolo` 一键绕过所有审批 |
| **绕过难度** | 难（无快捷绕过） | 易（`/yolo` 适合信任环境） |

**关键风险**：在需要合规审计的企业场景，Hermes 的 `/yolo` 可能是合规漏洞。OpenClaw 的 fail-closed 无法绕过，更适合监管环境。

---

## 五、凭证管理的根本差距

### Hermes：MCP 白名单模式

```
hermes mcp add git-server --allowed-tools read_file,write_file
```

**依赖 MCP server 本身可信**——如果 MCP server 被攻陷，白名单无法保护。

### OpenClaw：n8n Proxy Pattern（最被低估的设计）

```
OpenClaw → webhook call（无凭证）→ n8n（凭证锁定）→ External API
```

**凭证永不离开 n8n**：
- Agent 只做决策
- 凭证由 n8n 管理
- Agent 调用 webhook 时永远不接触 API key

**这是目前看到的唯一一个真正"凭证永不外泄"的架构设计。**

---

## 六、Secret Redaction 的差异

| | Hermes | OpenClaw |
|---|---|---|
| **脱敏方式** | 运行时文本替换（API Keys → [REDACTED]） | 容器隔离（防止 env var 泄露到执行环境） |
| **防护层面** | 输出层 | 环境层 |
| **目的** | 防止日志/响应中的敏感信息泄露 | 防止恶意环境变量注入 |

---

## 七、认证体系对比

| | OpenClaw | Hermes |
|---|---|---|
| **MCP** | 白名单模式 | MCP（白名单 + OAuth 2.1） |
| **OAuth** | 无 | MCP OAuth 2.1（文档不完整） |
| **n8n 集成** | n8n Proxy Pattern（凭证隔离） | 无对应设计 |
| **合规认证** | 无明确说明 | 无明确说明 |

---

## 八、安全哲学与目标用户的匹配

| | OpenClaw | Hermes |
|---|---|---|
| **目标用户** | 企业合规、金融、医疗、政府 | 个人/创业团队 |
| **配置复杂度** | 高（需专业安全知识） | 低（开箱即用） |
| **合规审计** | 更适合 | 可能需要额外配置 |
| **灵活性** | 低（fail-closed） | 高（callback + /yolo） |

---

## 知识断层清单

1. **Hermes dangerous_tools 的完整列表**：哪些工具被判定为危险？判定标准是什么？
2. **MCP OAuth 2.1 接入流程**：具体配置步骤是什么？
3. **OpenClaw critical 代码扫描实现**：基于什么技术（静态分析？AST）？
4. **数据泄露响应流程**：Agent 泄露数据后如何止损？
5. **合规认证**：OpenClaw/Hermes 是否有 SOC2/ISO27001 等认证？
6. **多租户隔离**：多用户共享实例时，记忆/凭证的隔离程度？

---

## 相关概念

- [[openclaw-2026-background-tasks-flows]] — OpenClaw 安全加固详情
- [[hermes-agent-source-code-architecture]] — Hermes 四个回调机制
- [[multi-agent-orchestration]] — n8n Proxy Pattern 详解
- [[openclaw-hermes-decision-tree]] — 选型决策树
