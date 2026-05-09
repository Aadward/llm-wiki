---
title: OpenClaw Flows CLI 与后台任务编排
created: 2026-05-11
updated: 2026-05-11
type: concept
tags: [openclaw, flows, background-tasks, orchestration, sqlite, task-management]
sources: [concepts/openclaw-2026-background-tasks-flows.md, concepts/openclaw-gateway-architecture.md]
confidence: medium
---

# OpenClaw Flows CLI 与后台任务编排

## 一、Flows CLI 的设计哲学

### 1.1 "线性"是有意选择

> "保持手动多任务流与单任务自动同步流分离"

"线性任务流"不是技术限制，而是**有意的哲学选择**：

- 非线性（DAG、依赖图）带来：状态爆炸、并发复杂度、调试困难
- 线性流满足的需求：用户需要"能看到任务跑到哪了"
- 复杂编排场景：由专业工具（n8n、Jenkins）处理，OpenClaw 不追求全覆盖

这与 OpenClaw"**The AI that actually does things**"的定位完全一致——在它做的事上做到极致可见可控。

### 1.2 三个核心命令

```bash
openclaw flows list    # 列出所有运行中的流
openclaw flows show    # 查看特定流详情
openclaw flows cancel  # 取消指定流
```

用户价值：**任务触发后不是"管不着"，而是随时可查看和干预**。

---

## 二、SQLite 分类账：任务感知的核心

### 2.1 完整生命周期

```
任务创建 → 后台运行 → SQLite 记录 → 任务感知 → 丢失恢复
                                              ↑
                                    Agent 重启后可继续执行
```

### 2.2 分类账的核心价值：可审计性

| 能力 | 用户价值 |
|------|----------|
| 统一生命周期 | cron / 子代理 / 手动触发，状态都在同一处 |
| 审计追踪 | 任何时刻任务状态可查 |
| 丢失恢复 | Agent 重启后任务不丢 |
| 维护可见性 | `flows show` 能看到任务卡在哪里 |

**本质是"运维思维"**：把任务当作有生命周期的东西来管理，而非一次性函数调用。

---

## 三、与 Hermes Cron 的根本性差异

| | OpenClaw Flows | Hermes Cron |
|---|---|---|
| **任务持久化** | SQLite 分类账，Agent 重启可恢复 | 无持久化，重启即丢失 |
| **可见性** | `flows list/show/cancel` 完整 UI | 无统一监控界面（黑的） |
| **触发统一性** | ACP/子代理/cron/CLI 统一接入 | cronjob 是独立工具 |
| **设计哲学** | 运维可见性优先 | 任务自动完成优先 |
| **自动修复** | `openclaw doctor` 自动修复损坏的流/任务 | 只有健康检查，无自动修复 |

**最关键差距**：Hermes 定时任务"触发后用户管不着"，OpenClaw"触发后随时可查看、取消、诊断"。

---

## 四、"doctor 修复"的设计智慧

> "为孤立或损坏的流/任务链接提供 `openclaw doctor` 修复"

这句话透露了一个重要认识：**在持久运行系统里，"任务损坏"是必然发生的常态，而非异常**。OpenClaw 没有假装可以避免，而是提供了自动修复机制。

对比：
- **OpenClaw**："不仅告诉你哪里有问题，还尽量自动修复"
- **Hermes**："告诉你哪里有问题，你自己处理"

---

## 五、OpenClaw 两条主线的汇合点

2026.3.11（安全加固）和 2026.4.5（多媒体）两条主线，在 Flows 这里是汇合的：

```
v2026.3.x（安全主线）
  └── trusted-proxy / 令牌管理 / 节点命令安全
        ↓
  后台任务安全性提升 ← Flows CLI 可审计

v2026.4.x（功能主线）
  └── 多媒体 / /dreaming / 任务进度可见
        ↓
  复杂任务可视化 ← Flows CLI 可追踪
```

Flows CLI 不是单独的功能迭代，而是两条主线的共同出口——安全和功能最终都需要"任务可见可控"。

---

## 六、知识断层清单

1. **flows 的 YAML schema**：步骤定义格式、参数传递方式均未知
2. **是否支持条件分支**：if/else、switch 等逻辑控制
3. **是否支持循环**：for/while 等迭代结构
4. **子步骤失败策略**：重试次数、超级设置、熔断机制
5. **与 heartbeat-state.json 的关系**：flows 和 heartbeat 状态是否共用同一 SQLite？
6. **`doctor` 修复的具体范围**：哪些类型的损坏可自动修复？

---

## 相关概念

- [[openclaw-gateway-architecture]] — Gateway 架构（含 Flows 在三层中的位置）
- [[openclaw-2026-background-tasks-flows]] — 后台任务系统原始文档
- [[openclaw-hermes-comparison]] — OpenClaw ↔ Hermes 对比（Flows vs Cron）
