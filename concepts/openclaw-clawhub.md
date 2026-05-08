---
title: ClawHub 技能生态详解
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [openclaw, clawhub, skills, marketplace, ecosystem]
sources: [web/blog.csdn.net/tzchao111/article/details/159697283, web/blog.csdn.net/qq_34252622/article/details/158965315]
confidence: high
---

# ClawHub 技能生态详解

## 概述

**ClawHub** 是 OpenClaw 的官方技能市场（Skills Marketplace），类似 npm install 的技能分发体验。截至 2026 年，ClawHub 已收录 **13,000+ 技能**，覆盖开发、运维、办公、数据分析等 30+ 场景。

---

## 一、核心数据

| 指标 | 数据 |
|------|------|
| 技能总数 | 13,000+ |
| 覆盖场景 | 30+ |
| 中国镜像 | 已上线（2026年4月） |
| 热门技能 | `find-skills`（87.6k 部署量） |

---

## 二、技能分类

### 2.1 Highlighted Skills（突出技能）

官方/社区精选，信号质量高，适合快速安装。

### 2.2 Popular Skills（热门技能）

下载量最高、无明显恶意行为的技能。

---

## 三、核心能力

### 3.1 一键安装

```bash
# 搜索可用 Skill
openclaw skill search "code-review"

# 安装社区 Skill
openclaw skill install @community/github-pr-reviewer

# 查看已安装 Skill
openclaw skill list
```

### 3.2 版本管理与依赖解析

```json
// openclaw.json 配置
{
  "skills": {
    "installed": [
      {
        "name": "@community/github-pr-reviewer",
        "version": "^1.2.0",
        "config": {
          "autoApprove": false,
          "reviewStyle": "thorough"
        }
      }
    ]
  }
}
```

### 3.3 安全评分与社区评价

每个技能都有安全评分和社区评价，帮助用户筛选高质量技能。

### 3.4 私有 Skill Registry

支持企业场景的私有技能仓库。

---

## 四、热门技能推荐

### 4.1 必备技能

| 技能 | 功能 | 安装量 |
|------|------|--------|
| `find-skills` | 语义搜索 + 智能路由，发现好技能 | 87.6k |
| `self-improving-agent` | 让智能体自我改进、变主动 | 高 |
| `Summarize` | 网页、PDF、图片、音频、YouTube 总结 | 高 |

### 4.2 开发辅助

| 技能 | 功能 |
|------|------|
| `github-pr-reviewer` | GitHub PR 审查 |
| `code-search` | 代码库搜索 |
| `docker-management` | Docker 容器管理 |
| `git-workflow` | Git 工作流自动化 |

### 4.3 办公效率

| 技能 | 功能 |
|------|------|
| `notion-sync` | Notion 待办同步到飞书 |
| `dingtalk-docs` | 钉钉文档集成 |
| `slack-notification` | Slack 通知 |

### 4.4 数据分析

| 技能 | 功能 |
|------|------|
| `data-analysis` | 数据分析 |
| `csv-transform` | CSV 转换 |
| `api-design` | API 设计 |

---

## 五、发布自定义技能

```bash
# 发布自定义 Skill 到 ClawHub
openclaw skill publish ./my-skill \
  --name "notion-sync" \
  --description "自动同步 Notion 待办到飞书"
```

---

## 六、ClawHub 生态特点

### 6.1 定位

ClawHub 是 OpenClaw 生态的**中心枢纽与分发引擎**，定义了技能的安装规范与版本控制协议。类似于苹果的 App Store。

### 6.2 安装方式

```bash
# 全局安装 clawhub CLI
npm i -g clawhub

# 安装后即可接入云端技能库
clawhub install <skill-name>
```

### 6.3 find-skills 技能

`find-skills` 是生态中最受欢迎的技能发现工具，累计部署量高达 87.6k。它充当系统的**语义搜索引擎与智能路由**，利用向量相似度匹配算法，能在用户输入模糊需求时，自动在 OpenClaw 社区库中检索功能重合度最高的插件。

---

## 七、中国镜像

2026 年 4 月，OpenClaw 官方推出 ClawHub 中国官方镜像站点，提升中国地区用户访问和使用技能库的体验。

---

## 八、与 Hermes Skills 对比

| 维度 | ClawHub | Hermes Skills |
|------|---------|---------------|
| 技能数量 | 13,000+ | 648+ |
| 定位 | 技能市场 + 执行引擎 | 闭环学习 + 自生成 |
| 管理方式 | 用户主导安装 | Agent 自动生成 |
| 生态类型 | 社区驱动 | 官方 + 社区 |

---

## 相关概念

- [[openclaw]] — OpenClaw 整体介绍
- [[openclaw-source-code-architecture]] — OpenClaw 源码架构
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比
- [[hermes-agent-skill-library]] — Hermes 技能库
