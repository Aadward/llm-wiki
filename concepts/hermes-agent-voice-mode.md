---
title: Hermes Agent Voice Mode 实战配置
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, voice, TTS, speech, audio, Telegram]
sources: [web/cloud.tencent.com/developer/article/2655556]
confidence: medium
---

# Hermes Agent Voice Mode 实战配置

## 概述

Hermes Agent 的 Voice Mode 主要通过 **TTS（Text-to-Speech）** 实现语音合成输出，支持多种 TTS 提供商。配合 Telegram 等消息平台的语音消息功能，可以实现完整的语音交互体验。

---

## 一、TTS 提供商

### 1.1 支持的 TTS 提供商

| 提供商 | 特点 | 费用 | 推荐场景 |
|--------|------|------|----------|
| **Edge-TTS** | 微软 Edge 在线语音，免费高质量 | 免费 | 国内用户首选 |
| **OpenAI-TTS** | OpenAI 官方 TTS，API 兼容 | 按量计费 | 已有 OpenAI API Key |
| **MiniMax-TTS** | 支持中文，音质好 | 按量计费 | 国内生产环境 |
| **Custom TTS** | 自定义 HTTP TTS 端点 | 自定 | 有自建 TTS 服务 |

### 1.2 Edge-TTS 配置（推荐国内用户）

Edge-TTS 利用微软 Edge 浏览器的在线语音服务，无需 API Key，完全免费。

```yaml
# ~/.hermes/config.yaml
tts:
  provider: edge
  voice: "zh-CN-XiaoxiaoNeural"  # 中文女声
  rate: "+0%"      # 语速调整
  volume: "+0%"    # 音量调整
```

**可用中文音色**：
| 音色名 | 特点 |
|--------|------|
| `zh-CN-XiaoxiaoNeural` | 中文女声，自然流畅（推荐） |
| `zh-CN-YunxiNeural` | 中文男声 |
| `zh-CN-XiaoyiNeural` | 中文女声，活泼 |
| `zh-CN-YunyangNeural` | 中文男声，播音风格 |

### 1.3 OpenAI-TTS 配置

```yaml
tts:
  provider: openai
  model: "tts-1"          # 或 tts-1-hd（高清）
  voice: "alloy"           # alloy, echo, fable, onyx, nova, shimmer
  api_key: "${OPENAI_API_KEY}"
```

### 1.4 自定义 TTS 端点

```yaml
tts:
  provider: custom
  endpoint: "http://your-tts-server:8080/tts"
  api_key: "${CUSTOM_TTS_API_KEY}"
```

---

## 二、Telegram 语音配置

### 2.1 Telegram Bot 语音消息

Telegram 支持接收和发送语音消息，配合 TTS 实现语音交互：

```yaml
# ~/.hermes/config.yaml
gateways:
  telegram:
    enabled: true
    bot_token: "${TELEGRAM_BOT_TOKEN}"
    allowed_users:
      - your_telegram_user_id
    voice_reply: true    # 自动将文本回复转为语音
    voice_format: "mp3"  # 或 ogg
```

### 2.2 语音消息接收

当用户发送语音消息时，Hermes 会：
1. 接收语音文件（`.ogg` 格式）
2. 使用 Whisper 自动转写为文本
3. 处理文本请求并生成响应
4. 使用 TTS 将响应转为语音发送回用户

```yaml
# 启用语音输入
voice_input:
  provider: whisper   # 或 cloudflare
  model: "base"       # tiny, base, small, medium, large
```

---

## 三、TTS + Gateway 集成架构

```
用户语音消息（TG）
    ↓
Telegram Gateway
    ↓
Whisper 语音识别 → 文本
    ↓
Hermes AIAgent（文本处理）
    ↓
TTS 语音合成（Edge-TTS / OpenAI）
    ↓
Telegram 语音消息回复
```

---

## 四、腾讯云方案（国内生产环境推荐）

腾讯云提供兼容 OpenAI-TTS 的 API 服务，适合国内环境：

```yaml
tts:
  provider: tencent
  model: "tts-1"
  voice: "zh-CN-XiaoxiaoNeural"
  app_id: "${TENCENT_APP_ID}"
  secret_id: "${TENCENT_SECRET_ID}"
  secret_key: "${TENCENT_SECRET_KEY}"
  endpoint: "tts.tencentcloudapi.com"
```

---

## 五、命令行语音测试

```bash
# 测试 TTS 是否正常
hermes tts test

# 测试特定音色
hermes tts test --voice "zh-CN-YunxiNeural"

# 查看可用音色列表
hermes tts voices
```

---

## 六、常见问题

| 症状 | 原因 | 解决 |
|------|------|------|
| TTS 无声音 | TTS 提供商配置错误 | 检查 `tts.provider` 和 API Key |
| Edge-TTS 超时 | 网络问题 | 配置代理或切换到国内 TTS |
| Telegram 语音无法识别 | Whisper 未配置 | 检查 `voice_input.provider` 配置 |
| 音色奇怪 | 选择了不合适的音色 | 尝试 `zh-CN-XiaoxiaoNeural` |

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-best-practices]] — 最佳实践（Gateway 部分）
- [[hermes-agent-mcp-integration]] — MCP 集成
