---
title: Hermes Agent 官方技能库系统
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, skills, agentskills, skill-library, skill-manager]
sources: [web/qiniushanghai.com, web/cloud.tencent.com/developer/article/2655680]
confidence: high
---

# Hermes Agent 官方技能库系统

## 概述

Hermes Agent 的 **Skills Hub** 是其最重要的扩展机制，目前收录 **648 个技能**（77 个内置、50 个官方可选、521 个社区贡献）。每个技能（Skill）是一个可复用的工具封装，安装后即可通过斜杠命令（`/skill-name`）调用。

---

## 一、技能库规模

| 类别 | 数量 | 说明 |
|------|------|------|
| 内置技能 | 77 | 出厂自带，覆盖核心功能 |
| 官方推荐 | 50 | 官方验证，推荐安装 |
| 社区贡献 | 521 | 社区共享，持续增长 |
| **总计** | **648+** | 持续增长中 |

---

## 二、技能分类（80 个官方推荐）

Hermes Agent v0.10.0 官方列出的 80 个推荐技能，覆盖以下领域：

### 2.1 软件工程（AI Lifecycle）

| 技能 | 功能 |
|------|------|
| `github-pr-workflow` | GitHub PR 完整流程（创建 → Review → 合并） |
| `github-code-review` | 自动代码审查 |
| `code-search` | 代码库搜索 |
| `docker-management` | Docker 容器管理 |
| `git-workflow` | Git 工作流自动化 |

### 2.2 个人生产力

| 技能 | 功能 |
|------|------|
| `email-management` | 邮件管理 |
| `calendar-scheduler` | 日程安排 |
| `note-taking` | 笔记管理 |
| `task-tracker` | 任务跟踪 |

### 2.3 数据与 DevOps

| 技能 | 功能 |
|------|------|
| `server-monitor` | 服务器监控（CPU/内存/磁盘） |
| `database-admin` | 数据库管理 |
| `docker-ops` | Docker 运维 |
| `log-analyzer` | 日志分析 |

### 2.4 内容与 SEO

| 技能 | 功能 |
|------|------|
| `content-seo` | SEO 内容优化 |
| `writing-assistant` | 写作辅助 |
| `translation` | 翻译 |

---

## 三、技能管理命令

```bash
# 浏览所有可用技能
hermes skills browse

# 搜索技能
hermes skills search <关键词>

# 安装技能
hermes skills install <skill-id>

# 预览技能内容（不安装）
hermes skills inspect <skill-id>

# 查看已安装技能
hermes skills list

# 更新全部技能
hermes skills update

# 卸载技能
hermes skills uninstall <skill-id>
```

---

## 四、安装示例

### 4.1 官方技能安装

```bash
# 安装 GitHub PR 工作流
hermes skills install official/github-pr-workflow

# 安装 Docker 管理
hermes skills install official/docker-management

# 安装服务器监控
hermes skills install community/server-monitor
```

### 4.2 社区技能安装

```bash
# 安装社区贡献的 SEO 优化技能
hermes skills install community/content-seo

# 安装 Git 工作流
hermes skills install community/git-workflow

# 安装数据库管理
hermes skills install community/database-admin
```

### 4.3 强制安装（跳过安全扫描）

某些技能会被安全扫描器标记为高危，需要 `--force` 参数：

```bash
hermes skills install --force community/skill-vetter
```

---

## 五、使用方式

### 5.1 CLI 中使用

```bash
# 启动 Hermes 后，直接输入斜杠命令
/hermes
> /github-pr-workflow 帮我为 auth 重构创建一个 PR
> /docker-management 列出所有运行中的容器
> /server-monitor 检查 CPU 使用率
```

### 5.2 Telegram/Discord 中使用

```
# 在 Telegram 或 Discord 中同样适用
/github-pr-workflow 帮我创建一个 PR
/docker-management 列出所有运行中容器
```

### 5.3 技能组合使用

技能可以组合使用，实现复杂工作流：

```
/github-pr-workflow 创建 PR
    ↓
/code-review 审查代码
    ↓
/docker-ops 部署到测试环境
```

---

## 六、官方精选 10 大必装技能

### 1. `github-pr-workflow` — GitHub PR 完整流程

覆盖从创建 branch 到提交 PR、添加 reviewer、写 commit message 的完整流程。

### 2. `docker-management` — Docker 容器管理

列出运行中的容器、查看日志、执行命令。

### 3. `server-monitor` — 服务器监控

CPU/内存/磁盘监控、进程管理、告警推送。

### 4. `git-workflow` — Git 工作流

自动化 PR Review、分支管理、Commit 消息规范检查。

### 5. `database-admin` — 数据库管理

SQL 查询辅助、表结构分析、性能优化建议。

### 6. `docker-ops` — Docker 运维

容器管理、镜像清理、DockerCompose 配置生成。

### 7. `content-seo` — SEO 内容优化

关键词分析、文章 SEO 检查、Meta 信息生成。

### 8. `log-analyzer` — 日志分析

日志聚合、异常检测、趋势分析。

### 9. `api-design` — API 设计

RESTful API 设计、OpenAPI 规范生成。

### 10. `security-scan` — 安全扫描

代码安全扫描、依赖漏洞检测。

---

## 七、agentskills.io 开放标准

Hermes Agent 兼容 **agentskills.io** 开放标准，这意味着：

- 社区创建的技能包可以直接导入
- 技能可以在不同 Agent 框架间共享
- 技能市场地址：https://github.com/agentskills/agentskills

---

## 八、技能加载机制

### 8.1 三级渐进式加载

```
第一级：Skill Index（技能索引）
  ↓
第二级：Skill Frontmatter（技能元数据）
  ↓
第三级：Skill Content（技能完整内容）
```

### 8.2 按需加载

技能不是每次都加载完整内容，而是按需展开，避免消耗过多 token。

---

## 九、自定义技能开发

### 9.1 技能结构

```
skills/
└── my-custom-skill/
    ├── SKILL.md          # 技能定义
    ├── references/       # 参考文档
    ├── templates/       # 模板文件
    └── scripts/         # 辅助脚本
```

### 9.2 SKILL.md 格式

```markdown
---
title: My Custom Skill
description: A skill that does something useful
trigger: /my-skill
category: utility
---

# My Custom Skill

## Usage

Describe how to use this skill...

## Examples

Provide usage examples...
```

---

## 十、最佳实践

- [ ] 根据用途选择性安装技能，避免加载过多
- [ ] 社区技能安装前先 `inspect` 预览内容
- [ ] 高危技能注意使用 `--force` 跳过安全扫描
- [ ] 定期执行 `hermes skills update` 更新技能
- [ ] 创建自定义技能前先浏览现有技能避免重复

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-skills-system]] — 技能系统（内置机制）
- [[hermes-agent-memory-architecture]] — 记忆架构
- [[hermes-agent-best-practices]] — 最佳实践
