---
title: Hermes Agent v0.12.0 版本解析
created: 2026-05-10
updated: 2026-05-10
type: concept
tags: [hermes, version-release, self-evolution, curator, skill-lifecycle]
sources: [raw/articles/hermes-agent-v0-12-release.md]
confidence: high
---

# Hermes Agent v0.12.0 版本解析

## 核心主题：The Curator Release

v0.12.0 发布于 **2026年4月30日**，代号 **"The Curator Release"**。这是 Hermes Agent 历史上最大规模的一次社区驱动更新。

## 规模数据

| 指标 | 数值 |
|------|------|
| Commits | 1,096 |
| Merged PRs | 550 |
| Files Changed | 1,270 |
| Insertions | 217,776 |
| Community Contributors | 213（含合著者） |

相比 v0.11.0 是一次超大规模更新，社区参与度极高。

## 核心新功能：自主 Curator

Hermes Agent 现在可以**自主维护自身**。一个 autonomous background Curator 在后台自动运行，负责：

- **Grading（评级）** — 对技能质量进行评估打分
- **Pruning（剪枝）** — 删除低质量/冗余的技能
- **Consolidating（整合）** — 合并相似技能，减少重复

Curator 按设定的时间表自动运行，形成真正的**自我进化闭环**：
- 任务执行 → 经验提取 → 技能生成 → 使用中评估 → Curator 剪枝整合 → 更高质量技能 → 更好的任务执行

这是对之前 v0.8/v0.10 版本 skill_manage 系统的实质性升级。

## 版本演进脉络

| 版本 | 日期 | 核心功能 |
|------|------|----------|
| v0.8.0 | 2026-04-08 | Live Model Switching、后台任务通知 |
| v0.10.0 | 2026-04-16 | Nous Tool Gateway（搜索、图片、TTS、浏览器自动化） |
| v0.12.0 | 2026-04-30 | **Autonomous Curator**、自我维护 |

v0.12.0 的发布意味着 Hermes Agent 从"具备自我进化能力"进化到"能够自主维护和优化自身技能库"。

## ⚠️ 争议事件：EvoMap 抄袭指控

**2026年4月15日**，中国 AI 团队 EvoMap 公开指控 Hermes Agent 的核心自进化功能抄袭了其开源项目 **Evolver**。

### 核心指控
EvoMap 发布的详细技术对比报告显示两者存在**"结构性同构"**：
- Evolver 的 10 步进化主循环 ↔ Hermes 自进化模块的 10 步执行流程一一对应
- 12 组核心术语系统性替换（Gene→SKILL.md, Capsule→技能执行记录, solidify→skill_manage 等）
- 三层记忆体系高度相似（持久事实层 + 程序性记忆层 + 历史搜索层）

### 关键时间线
- **2025年12月**：EvoMap Evolver 最早 commit（有记录）
- **2026年02月01日**：EvoMap 公开开源 Evolver
- **2026年03月09日**：Hermes Agent 自进化子仓库创建
- **2026年03月12日**：Hermes v0.2.0 发布（带自进化功能）
- **2026年04月15日**：EvoMap 发表详细指控报告

### Nous Research 回应
Hermes 官方回应为：**"我们的仓库2025年7月就有了。我们是先驱。Delete your account。"** 并拉黑了 EvoMap 团队成员。

### 争议焦点
- 私有仓库内容无法独立验证，不能作为原创证据
- 被指控的 7 个同构特征全部集中在 3月9日 创建的自进化子仓库
- 时间线存在 36 天的差距

**截至 wiki 更新（2026-05-10），此争议尚无定论。**

## 相关页面
- [[hermes-agent-v0-8-v0-10-release]] — v0.8/v0.10 版本解析
- [[hermes-agent-skill-library]] — 技能系统官方生态
- [[memory-knowledge-systems]] — 记忆系统系列（含 OpenClaw 方案对比）
- [[hermes-agent-evolution-controversy]] — Evolver 抄袭事件详细记录（待创建）
