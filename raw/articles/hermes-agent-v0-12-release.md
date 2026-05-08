---
source_url: https://so.html5.qq.com/page/real/search_news?docid=70000021_84569f8c4b073552
ingested: 2026-05-09
sha256: dd4cb66d9ab69883bd0dbb7bd62b437244d467082614a5a3e183885bcd6d5b98
---

# Hermes Agent v0.12.0 发布详情

## 发布信息
- **版本**: v0.12.0
- **发布日期**: 2026-04-30
- **官方来源**: https://so.html5.qq.com/page/real/search_news?docid=70000021_84569f8c4b073552

## v0.12.0 核心变化

> 注意：v0.11.0（2026-04-23）带来了 React/Ink TUI 重写、Transport 架构、GPT-5.5、QQBot、插件系统、/steer 中途纠偏等重大更新。v0.12.0 在此基础上继续迭代。

### 界面与交互
- **多界面并行**：Hermes 不限制单一交互界面，CLI / Gateway / API Server 可同时运行
- **斜杠指令体系完整化**：
  - `/new` 新会话
  - `/reset` 重置
  - `/retry` 重试
  - `/undo` 撤销
  - `/copy [N]` 复制回复
  - `/image` 附图
  - `/paste` 粘贴板图片
  - `/voice` 语音模式
  - `/browser` CDP 浏览器连接
  - `/history` 会话历史
  - `/save` 保存会话
  - `/help` 帮助

### 会话控制
- 支持多会话并行管理
- `/branch` 分支会话
- `/goal` 设置持续目标
- `/background` 后台任务

### 配置类命令
- `/model [name]` 切换模型
- `/personality [name]` 设定人格
- `/reasoning [level]` 推理深度
- `/toolsets` 工具管理
- `/config` 配置查看

### 渠道与网关
- `/approve` / `/deny` 审批
- `/restart` 重启网关
- `/sethome` 设为主频道
- `/update` 更新 Hermes
- `/platforms` 平台状态

### 安全性
- `/yolo` 命令审批绕过
- `/security` 安全状态
- TUI 中的审批提示更清晰

### 消息类（Gateway）
- 支持 Telegram、Discord、Slack、WhatsApp、Signal、Matrix、Email、SMS、飞书、钉钉、企业微信等多平台

## 版本演进参考
- **v0.8.0** (2026-04-08): Live Model Switching、后台任务通知、Google AI Studio 原生 Provider
- **v0.9.0** (2026-04-13): 本地 Web Dashboard、Fast Mode、iMessage/WeChat、Android Termux 支持
- **v0.10.0** (2026-04-16): Nous Tool Gateway（搜索、图片生成、TTS、浏览器自动化）
- **v0.11.0** (2026-04-23): React/Ink TUI 重写、Transport 架构、GPT-5.5、QQBot、插件系统、/steer 中途纠偏
- **v0.12.0** (2026-04-30): 斜杠指令体系完整化、多会话并行、更多配置选项
