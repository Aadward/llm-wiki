---
title: Hermes Agent EvoMap 抄袭争议
created: 2026-05-10
updated: 2026-05-10
type: concept
tags: [hermes, controversy, open-source-ethics, evomap, evolver, self-evolution]
sources: [web research 2026-04-16]
confidence: medium
contested: true
contradictions: [hermes-agent-skill-library, hermes-agent-v0-12-release]
---

# Hermes Agent EvoMap 抄袭争议

## 事件概述

**2026年4月15日**，中国 AI 团队 **EvoMap** 公开指控 **Hermes Agent**（Nous Research）的核心自进化功能抄袭了其开源项目 **Evolver**。这是 2026 年开源 AI 领域最引人关注的争议事件之一。

## EvoMap 的核心指控

EvoMap 在 X（原 Twitter）和官网发布了详细的技术对比报告，核心指控为**"架构级抄袭"**（结构性同构），而非简单的代码复制：

### 1. 10步主循环一一对应
- Evolver 的 10 步进化主循环
- Hermes 自进化模块的 10 步执行流程
- 逻辑上一一对应，连顺序都高度一致

### 2. 12组核心术语系统性替换
| Evolver 术语 | Hermes 术语 |
|-------------|------------|
| Gene | SKILL.md |
| Capsule | 技能执行记录 |
| solidify | skill_manage (create) |
| Evolution | Self-Evolution |
| ... | ... |

### 3. 三层记忆体系雷同
双方均采用高度相似的三层记忆架构：
- **持久事实层**（persistent factual memory）
- **程序性记忆层**（procedural memory）
- **历史搜索层**（historical search）

### 4. 共同的闭环范式
- 任务完成后自动提取可复用资产
- 周期性自我评估与反射机制
- 技能在使用中自我改进

## 关键时间线

| 日期 | 事件 |
|------|------|
| 2025年12月 | EvoMap Evolver 最早 commit（有 GitHub 记录） |
| 2026年02月01日 | EvoMap 公开开源 Evolver + GEP 协议 |
| 2026年03月09日 | Hermes Agent 自进化子仓库创建 |
| 2026年03月12日 | Hermes v0.2.0 发布（带自进化功能） |
| 2026年04月08日 | Hermes v0.8.0 发布 |
| 2026年04月15日 | EvoMap 发布详细技术指控报告 |
| 2026年04月16日 | Hermes 官方回应，EvoMap 成员被拉黑 |
| 2026年04月30日 | Hermes v0.12.0 发布（Curator 功能） |

## Hermes/Nous Research 回应

> "我们的仓库2025年7月就有了。我们是先驱。Delete your account."

官方账号在 EvoMap 团队成员的社交平台下回复上述内容，随后拉黑了对方。

## 争议焦点

1. **私有仓库无法验证**：Hermes 声称2025年7月就有相关代码，但私有仓库内容无法独立验证
2. **子仓库时间线问题**：被指控的7个同构特征全部集中在2026年3月9日才创建的自进化子仓库
3. **Evolver 的公开知名度**：Evolver 在 ClawHub 上线10分钟登热门榜首，前3天下载量超3.6万，并非寂寂无闻

## 事件影响

- 引发开源社区对"AI洗代码"（用AI工具重写代码掩盖来源）问题的广泛讨论
- 涉及中美 AI 开源生态的信任问题
- Hermes 随后在 2026-04-30 发布 v0.12.0（Curator Release），争议期间持续更新

## 相关页面
- [[hermes-agent-v0-12-release]] — v0.12.0（争议后发布的版本，含 Autonomous Curator）
- [[hermes-agent-skill-library]] — 技能系统官方生态（相关争议功能）
- [[memory-knowledge-systems]] — 记忆系统对比（三层记忆体系对比）

## 重要声明

⚠️ **截至本 wiki 更新（2026-05-10），此争议尚无最终定论。** 本页面仅记录事件经过，不代表任何一方的立场。所有指控和回应均基于公开资料整理。
