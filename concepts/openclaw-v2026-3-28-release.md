---
title: OpenClaw v2026.3.28 版本发布
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [openclaw, release, security, reliability]
sources: [raw/articles/openclaw-v2026-3-28-release.md]
confidence: high
---

# OpenClaw v2026.3.28 版本发布

## 核心定位

版本跃迁:v2026.3.24 to v2026.3.28,含多项高危漏洞修复,**建议立即更新**。

## 安全更新:多项高危漏洞修复

### 浏览器控制安全加固

**文件上传/下载路径遍历漏洞**

- **问题:** 攻击者可通过构造特殊路径字符串,突破 OpenClaw temp 目录限制,读写任意文件
- **修复:** 限制输出路径到 OpenClaw temp 根目录,阻止路径遍历/逃逸
- **影响:** 所有使用浏览器自动化功能的用户

这是 v2026.3.23 以来安全加固路线的重要组成部分。

## Agents 可靠性提升

- 子智能体任务调度稳定性增强
- 任务卡死问题修复

## Memory 优化

- QMD 搜索结果精确度提升
- 跨进程嵌入运行优化,避免多代理场景下的惊群效应

## 关联概念

- [[security-model-comparison]] - OpenClaw 安全模型
- [[openclaw-gateway-architecture]] - Gateway 架构
