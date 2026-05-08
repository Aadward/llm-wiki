---
source_url: https://github.com/hesamsheikh/awesome-openclaw-usecases
ingested: 2026-05-09
sha256: 0294dbc9c9e839662edef03589ef73841a5a0f8c86b2984306c9422411171d6f
---

# Hermes Agent 官方概览（2026）

来源：hermes-agent.nousresearch.com 官方首页及文档

## 核心理念

Hermes Agent 定位为"The Agent That Grows With You"——不是一次性工具，而是一个随使用不断进化的持久化个人代理。

核心差异化：
1. **闭环学习** — 内置 GEPA 引擎，自动评估、优化、沉淀成功经验
2. **四层记忆** — Working → Episodic → MEMORY.md → USER.md
3. **技能沉淀** — 自动将解题模式保存为可复用 Skill
4. **多平台网关** — Telegram/Discord/Slack/WhatsApp 等 10+ 平台统一接入
5. **模型无关** — 20+ LLM 提供商灵活切换

## 六大核心特性

| 特性 | 说明 |
|------|------|
| Lives Where You Do | 持久记忆 + 自动生成 Skills |
| Grows the Longer It Runs | 自然语言 Cron 调度，无人值守自动化 |
| Scheduled Automations | 定时任务、报告、备份、每日简报 |
| Delegates & Parallelizes | 隔离子代理，零上下文代价并行流水线 |
| Real Sandboxing | 5 种运行后端，容器硬化 |
| Full Web & Browser Control | 网页搜索、浏览器自动化、视觉识别、图像生成、TTS |

## 40+ 内置工具

信息获取：web_search, web_extract, 浏览器自动化
文件代码：write_file, read_file, terminal, git, patch, pytest
记忆任务：session_search, task_planning, cron scheduling
AI 能力：image_generation, text_to_speech, multi_model_reasoning, subagent_delegation

## 技术规格

- Python >= 3.11
- 支持 Local/Docker/SSH/Daytona/Singularity/Modal 多种执行后端
- MIT 许可证
- 活跃开发中
