---
source_url: https://www.cnblogs.com/qiniushanghai/p/19851330
ingested: 2026-05-12
sha256: d9e8f7a6b5c4d3e2f1a0b9c8d7e6f5a4b3c2d1e0f9a8b7c6d5e4f3a2b1c0d
---

# Hermes Agent v0.8.0 / v0.10.0 版本解析

**版本:** v0.8.0(2026年4月8日) / v0.10.0(2026年4月16日)
**升级类型:** 功能增强
**信息源:** 七牛云博客 / CSDN

---

## v0.8.0 核心功能

### Live Model Switching(实时切换)
在 Telegram/Discord/Slack 等消息平台的对话中,无需重启即可实时切换底层 LLM。

### hermes model 向导
通过统一的 `hermes model` 向导配置任意 LLM 提供商,无需手动编辑配置文件。

### 支持的提供商
- Nous Portal(原生 Hermes 系列)
- OpenRouter(200+ 模型统一接入)
- OpenAI
- Kimi(国内直连)
- MiniMax(国内直连)

### Live Model Switching 价值
无需重启网关,在对话中直接切换模型,动态测试不同模型能力。

---

## v0.10.0 补充信息

- Windows 安装教程支持(WSL2)
- 进一步稳定性和兼容性提升
