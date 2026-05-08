---
source_url: https://so.html5.qq.com/page/real/search_news?docid=70000021_63269c1ee7e52652
ingested: 2026-05-08
sha256:c2290f78c288b610efbb24e608a6033dc5774037bea9db2bff063f9a8a0bf1e4
---

# OpenClaw 大版本更新：全面接入 GPT-5.4 与 Claude Vertex

## 核心变更：插件系统"换骨"

本次更新最核心的变动是**彻底重构了插件系统**：

- 旧的扩展 API（`openclaw/extension-api`）被**完全移除**
- 取而代之的是全新的模块化 **plugin-sdk**
- 旧插件需要完全重写才能在 2026.3+ 版本运行

## 其他重要更新

### 模型支持
- 新增 GPT-5.4 原生支持
- 新增 Claude Vertex 支持
- 新增 Claude Opus 4.6 支持（2026.2.23）
- 新增 Sonnet 4.6 深度集成，支持 1M 超长上下文（2026.2.17）
- 新增 Gemini 3.1 Flash / Gemini 3.1 Flash-Lite 支持（2026.3.7）

### Context Engine（2026.3.7）
- 创新的可插拔上下文引擎
- 允许开发者通过插件接口自定义上下文处理逻辑
- 无需修改核心代码

### 安全更新（2026.2.23）
- 浏览器 SSRF 策略默认调整为 "trusted-network" 模式
- 私有网络用户需显式配置
- 可通过 `openclaw doctor --fix` 迁移旧版设置
