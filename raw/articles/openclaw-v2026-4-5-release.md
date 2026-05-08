---
source_url: https://so.html5.qq.com/page/real/search_news?docid=70000021_08769d3856e78952
ingested: 2026-05-09
sha256: c0161234dfd79157573f8314b56cbbd363e9a1859a37243695800a6be8913df1
---

# OpenClaw v2026.4.5 官方发布详情

## 发布信息
- **版本**: v2026.4.5
- **发布日期**: 2026-04-06
- **官方来源**: https://github.com/openclaw/openclaw/releases/tag/v2026.4.5

## 核心新功能

### 1. 多媒体生成正式进入核心 (Multimedia Generation)
- 图片生成（GAI/Gemini 等）已从实验性功能升级为核心内置能力
- 音频/语音合成能力补全
- /image 命令全面可用，支持多种后端

### 2. /dreaming 从实验走向可用
- /dreaming 原本是实验性的 AI 画图/创作模式
- v2026.4.5 中已成熟，提供更好的 prompt 优化和结果质量
- 支持分步预览和迭代优化

### 3. 复杂任务分步进度可见 (Step-by-Step Progress)
- 长时任务（代码生成、数据处理、多步骤工作流）现在能看到具体步骤
- 每一步有清晰的状态指示器
- 任务可暂停、恢复、取消

### 4. Prompt Cache 更稳定、更省钱
- 改善了与 Claude/GPT 等 provider 的缓存命中率
- 减少重复 token 消耗，降低使用成本
- 缓存策略更智能，自动管理 TTL

### 5. 多语言支持补全
- 控制台（Console）和文档全面支持多语言
- 中文体验优化（中文文档、错误提示本地化）

### 6. Anthropic 政策变动正面应对
- 针对 Claude API 使用政策变化做了适配
- 调整了 tool use 行为，确保合规
- 改进了引用（citation）提取逻辑

## 技术细节
- CLI 新增 `--progress` 标志用于显示任务步骤
- `/dreaming` 命令新增 `--quality` 和 `--style` 参数
- 媒体生成结果可直接通过 `/save` 命令存入 workspace
