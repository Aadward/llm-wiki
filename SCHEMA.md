---
title: Wiki Schema
created: 2026-05-08
updated: 2026-05-08
type: schema
---

# Wiki Schema

## Domain

**个人知识管理 & AI 工具学习** — 专注于 AI 工具的使用方法、交叉比较、经验沉淀。学习过程中的工具对比、实战技巧、工作流设计。

## Conventions

### 命名
- 文件名：小写、连字符、无空格（如 `openclaw-best-practices.md`）
- 目录名：小写、复数（如 `raw/articles/`）

### 页面约定
- 每个 wiki 页面以 YAML frontmatter 开头
- 使用 `[[wikilinks]]` 链接到其他页面，每页至少 2 个出站链接
- 更新页面时必须更新 `updated` 日期
- 新页面必须添加到 `index.md` 对应章节
- 所有操作必须追加到 `log.md`

### 来源标注
- 综合 3+ 来源的页面，在每个 claims 段落后附加 `^[raw/articles/source-file.md]` 溯源标记
- 单来源页面 `sources:` frontmatter 足够

## Frontmatter

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
# Optional:
confidence: high | medium | low
contested: true
contradictions: [other-page-slug]
---
```

## Tag Taxonomy

### 工具类型
- `agent` — AI Agent 框架/平台（OpenClaw、Claude Code、Codox 等）
- `skill` — 技能/插件/扩展（clawhub skills、MCP servers）
- `platform` — 消息平台集成（Telegram、Discord、WhatsApp 等）

### 活动类型
- `workflow` — 工作流设计
- `memory` — 记忆/知识管理
- `automation` — 自动化/Cron 任务
- `security` — 安全/凭证管理
- `research` — 研究/调研

### 主题类型
- `productivity` — 生产力工具
- `content` — 内容创作
- `infra` — 基础设施/DevOps
- `comparison` — 对比分析

### 元信息
- `meta` — wiki 本身（元信息、更新记录）
- `learning` — 学习过程/经验总结
- `pitfall` — 避坑指南

## Page Thresholds

- **创建页面**：某个工具/概念出现在 2+ 来源中，或在一个来源中是核心主题
- **追加到现有页面**：来源提到的内容已有页面覆盖
- **不创建**：仅一次性提及、 minor details、偏离领域的内容
- **拆分页面**：超过 ~200 行时，按子主题拆分并交叉链接
- **归档**：内容完全过时时移至 `_archive/`，从 index.md 删除

## Entity Pages

每个 notable 实体（工具/平台/人）一页，包括：
- 概述 / 是什么
- 关键事实和日期
- 与其他实体的关系（`[[wikilinks]]`）
- 来源引用

## Concept Pages

每个概念/主题一页，包括：
- 定义 / 解释
- 当前认知状态
- 开放问题或争议
- 相关概念（`[[wikilinks]]`）

## Comparison Pages

对比分析页，包括：
- 对比对象及原因
- 对比维度（表格格式优先）
- 结论或综合判断
- 来源

## Update Policy

新信息与现有内容冲突时：
1. 检查日期 — 较新的来源通常覆盖较旧的
2. 真正矛盾时，注明双方立场和来源
3. 在 frontmatter 中标记：`contradictions: [page-name]`
4. 在 lint 报告中标记给用户审核

## Archive Policy

1. 创建 `_archive/` 目录（如不存在）
2. 移动页面到 `_archive/`（保留原始路径，如 `_archive/entities/old-page.md`）
3. 从 `index.md` 删除
4. 更新链接到它的页面 — 将 wikilink 替换为纯文本 + "(archived)"
5. 记录到 log.md