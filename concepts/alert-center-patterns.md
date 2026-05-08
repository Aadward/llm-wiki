---
title: Alert Center Patterns
summary: 基于 LLM 的智能告警中心：告警归并、根因分析、自动定位、团队路由
tags: [agent, automation, infra, concept]
sources: [raw/articles/openclaw-cookbook-2026-02.md, raw/articles/self-healing-home-server.md]
created: 2026-05-08
---

# Alert Center Patterns

传统监控：Alert → 通知 → 人工排查（被动）
LLM Alert Center：Alert → 主动分析 + 定位 → 建议/自动修复 → 团队只做决策

## Architecture

```
Alert Sources (Prometheus/ELK/Webhooks)
         │
         ▼
┌─────────────────────────┐
│   LLM Triage Engine     │
│  - Deduplication        │
│  - Priority Re-rank     │
│  - Correlation          │
└────────────┬────────────┘
             │
    ┌────────┼────────┐
    ▼        ▼        ▼
 Ignored  Notify  Investigate
    │        │        │
    │        │   ┌────┴────┐
    │        │   │ Query   │
    │        │   │ Prometheus │
    │        │   │ Query ELK │
    │        │   │ Match KB  │
    │        │   └─────────┘
    │        │        │
    │        │   ┌────▼──────────┐
    │        │   │ Root Cause +  │
    │        │   │ Action Report │
    │        │   └──────────────┘
    │        │        │
    └────────┴────────┴──→ Action Layer
                          (Auto-fix / Human Approval)
```

## LLM Capabilities in Alert Flow

| LLM 擅长 | LLM 不擅长 |
|----------|-----------|
| 跨指标关联分析 | 直接修改生产配置 |
| 日志异常模式识别 | 高风险操作（需要确定性）|
| 生成诊断决策树 | 实时流数据处理 |
| 从历史 Incident 找相似 case | 精确数值计算 |
| 总结并用自然语言解释 | 保证 100% 准确 |

## Tiered Response

**Tier 1 — Triage（自动）**
- 去重：10 个监控点告警 → 1 条
- 聚合：同类事件合并
- 路由：按服务/severity 发给对应团队

**Tier 2 — Investigation（LLM 驱动）**
- 查询 Prometheus 指标趋势
- 查询 ELK 日志（时间窗口内 ERROR 日志）
- 匹配知识库（历史类似 Incident）
- 生成根因假设 + Confidence + 建议操作

**Tier 3 — Action（分层）**
- 已知模式（CPU 高 → 重启 Pod）→ 自动执行 + 日志记录
- 新模式 → 人工审批 + 结构化摘要推送

## Key Insight: Knowledge Compounding

```
每次 Incident 解决后：
  LLM 自动提炼 → 根因 / 诊断路径 / 操作记录
              → 入 RAG 知识库

下次类似告警：
  LLM 先查知识库 → 匹配到相似 case
               → 直接复用诊断路径
               → MTTR 从 30min → 5min
```

## Implementation: Prometheus + ELK Integration

**Alertmanager webhook 入口：**
```yaml
receivers:
  - name: 'llm-alert-agent'
    webhook_configs:
      - url: 'http://agent:8080/webhook/alertmanager'
```

**Prometheus API 查询趋势：**
```bash
curl -G "http://prometheus:9090/api/v1/query_range" \
  --data-urlencode 'query=rate(http_requests_total{service="api-gateway"}[5m])' \
  --data-urlencode 'start=...'
```

**ELK API 查错误日志：**
```bash
curl -X POST "http://elasticsearch:9200/logs-*/_search" \
  -d '{"query":{"bool":{"must":[
    {"match":{"service":"api-gateway"}},
    {"match":{"level":"ERROR"}},
    {"range":{"@timestamp":{"gte":"...","lte":"..."}}}
  ]}}}'
```

## Related

- [[openclaw]] — the platform
- [[persistent-agent-patterns]] — the agent architecture this runs on
- [[scheduled-automation]] — cron patterns that drive proactive monitoring
- [[security-credential-management]] — security for agents with infrastructure access
