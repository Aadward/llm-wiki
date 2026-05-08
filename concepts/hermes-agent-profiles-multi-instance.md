---
title: Hermes Agent Profiles 多实例管理与多 Agent 协作
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, profiles, multi-instance, configuration, HERMES_HOME]
sources: [web/github.com/cclank/Hermes-Wiki, web/blog.csdn.net/hanipapa540/article/details/160560684]
confidence: high
---

# Hermes Agent Profiles 多实例管理与多 Agent 协作

## 概述

Hermes Agent 的 **Profile** 是核心的多实例隔离机制——每个 Profile 是一个完全独立的 `HERMES_HOME` 目录，拥有自己的配置、记忆、会话、技能、网关和定时任务。通过 Profile，用户可以在同一台机器上运行多个互不干扰的 Agent 实例。

---

## 一、Profile 核心概念

### 1.1 什么是 Profile？

Profile 本质上是一个**命名的工作区隔离**。当你切换 Profile 时，Hermes 会切换整个 `~/.hermes/profiles/<name>/` 目录，所有配置、记忆、技能都完全独立。

```
~/.hermes/
├── config.yaml              # 默认主配置（可被 Profile 覆盖）
├── .env                     # 全局环境变量
├── profiles/                # Profile 目录
│   ├── work/               # 工作用 Profile
│   │   ├── config.yaml
│   │   ├── .env
│   │   ├── memories/
│   │   ├── skills/
│   │   ├── state.db
│   │   └── sessions/
│   ├── dev/                # 开发用 Profile
│   └── research/           # 研究用 Profile
```

### 1.2 Profile vs 默认配置

| 维度 | 默认配置 | Profile |
|------|---------|---------|
| 配置位置 | `~/.hermes/` | `~/.hermes/profiles/<name>/` |
| 隔离程度 | 共享所有数据 | 完全独立 |
| 适用场景 | 单人日常使用 | 多角色/多项目隔离 |
| 切换方式 | 无需切换 | `hermes profile switch <name>` |

---

## 二、配置层次（优先级）

Hermes 的配置按优先级从低到高排列：

1. **硬编码默认值**（`hermes_cli/config.py` 的 `DEFAULT_CONFIG`）
2. **用户配置文件**（`~/.hermes/config.yaml`）
3. **环境变量**（`.env` 文件 + shell 环境变量）
4. **CLI 参数**（`--model`, `--provider` 等命令行参数）
5. **Profile 覆盖**（`HERMES_HOME` 环境变量指向不同目录）

---

## 三、配置文件职责

Hermes 有两套配置文件，职责不同：

| 文件 | 存什么 | 生效方式 |
|------|--------|---------|
| `.env` | API Keys、敏感凭证 | 环境变量注入 |
| `config.yaml` | 运行时行为配置 | `load_config()` 读取 |

---

## 四、Profile 管理命令

```bash
# 列出所有 Profile
hermes profile list

# 创建新 Profile
hermes profile create <name>

# 切换到某个 Profile
hermes profile switch <name>

# 查看当前 Profile
hermes profile show

# 删除 Profile
hermes profile delete <name>

# 导出 Profile 配置
hermes profile export <name> --path ./backup/

# 导入 Profile
hermes profile import ./backup/<name>/
```

---

## 五、多实例使用场景

### 5.1 场景一：工作与个人分离

```bash
# 创建两个 Profile
hermes profile create work
hermes profile create personal

# 工作 Profile 使用 GPT-4o + 正式语气
# 个人 Profile 使用 Claude + 轻松语气
```

### 5.2 场景二：多项目隔离

```bash
# 每个项目一个 Profile
hermes profile create project-alpha
hermes profile create project-beta

# 每个 Profile 加载不同的技能和记忆
```

### 5.3 场景三：多语言模型对比

```bash
# 对比不同模型的效果
hermes profile create gpt4o-test
hermes profile create claude-test
hermes profile create deepseek-test
```

---

## 六、多 Agent 协作（Kanban 模式）

Hermes 支持通过 **Kanban 模式**实现多 Agent 协作：

### 6.1 Kanban 多 Agent 架构

```
┌─────────────────────────────────────────────┐
│            Hermes Kanban Orchestrator         │
├─────────────┬─────────────┬─────────────────┤
│  Agent-1    │  Agent-2    │  Agent-3         │
│ (Research)  │  (Coding)   │  (Review)        │
│ Profile:A   │  Profile:B  │  Profile:C       │
└─────────────┴─────────────┴─────────────────┘
```

### 6.2 任务分配配置

```yaml
# config.yaml
kanban:
  enabled: true
  agents:
    - name: research
      profile: research-profile
      role: "信息收集与调研"
    - name: coding
      profile: coding-profile
      role: "代码实现与测试"
    - name: review
      profile: review-profile
      role: "代码审查与优化"
```

### 6.3 任务流转

1. 用户在 Kanban 看板创建任务
2. Orchestrator 将任务分配给对应 Agent
3. Agent 执行并更新任务状态
4. 任务在 Research → Coding → Review 之间流转
5. 最终由 Review Agent 验收

---

## 七、Profile 环境变量

```bash
# 直接指定 HERMES_HOME（等同于切换 Profile）
HERMES_HOME=~/.hermes/profiles/work hermes chat

# 临时以某个 Profile 运行命令
hermes --hermes-home ~/.hermes/profiles/dev run "帮我写一个 Python 脚本"

# 查看当前 Profile 信息
hermes info
```

---

## 八、最佳实践

- [ ] 为不同用途创建独立 Profile，避免记忆污染
- [ ] Profile 名称使用有意义的命名（如 `work`, `research`, `coding`）
- [ ] 定期备份重要 Profile 的配置和记忆
- [ ] 敏感操作前确认当前 Profile
- [ ] 使用 `hermes profile export` 定期备份

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-best-practices]] — 最佳实践
- [[hermes-agent-memory-architecture]] — 记忆架构
- [[hermes-agent-skills-system]] — 技能系统
