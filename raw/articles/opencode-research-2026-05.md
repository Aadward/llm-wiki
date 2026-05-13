---
title: OpenCode 调研原始素材
summary: 2026-05-13 调研 OpenCode CLI 服务器部署和生产 Agent 工作流的多源素材汇总
tags: [research, agent]
created: 2026-05-13
updated: 2026-05-13
type: raw
sources: [self]
confidence: high
---

# OpenCode 调研原始素材

> 调研日期：2026-05-13
> 调研主题：OpenCode CLI 服务器部署 + Long-Running Agent 生产工作流
> 调研者：Hermes Agent

## 来源列表

### 一、官方来源（直接访问）

1. **opencode.ai 官网** — https://opencode.ai/
   - 安装命令 `curl -fsSL https://opencode.ai/install | bash` 直接展示
   - GitHub Stars: 150K（官网实时）
   - 功能特性：CLI / TUI / Server / Web / Desktop / IDE

2. **opencode.ai/docs** — https://opencode.ai/docs
   - 官方文档：Install / Usage(TUI/CLI/Web/IDE) / Configure / Develop 各章节
   - 明确列出 `--port`, `--hostname`, `--cors` 等服务器参数

3. **GitHub 仓库** — https://github.com/anomalyco/opencode
   - 实时数据：159k Stars, 18.6k Forks, 12,771 Commits（2026-05-13）
   - 504 Branches, 1020 Tags
   - 最新 commit: 12 minutes ago（调研时）

4. **opencode.ai/docs - Server** — https://opencode.ai/docs
   - Develop → Server 章节
   - 明确说明：运行 `opencode serve` 启动无头 HTTP 服务器

### 二、第三方独立文档站

5. **opencodeguide.com** — https://opencodeguide.com/zh/docs/develop/server
   - **独立性**：非 opencode.ai 官方，但内容高度一致
   - 服务器参数表格：
     ```
     --port      默认 4096
     --hostname  默认 127.0.0.1
     --mdns      默认 false
     --cors      默认 []
     ```
   - 身份验证：`OPENCODE_SERVER_PASSWORD` 环境变量
   - OpenAPI 规范：`http://host:port/doc`
   - API 端点：`/global/health`, `/global/event`, `/project` 等

### 三、社区博客（多源交叉）

6. **极客日志 zeeklog.com**（2026-03-29）
   - 标题：OpenCode: 开源版 Claude Code 体验与配置指南
   - 明确写了 `opencode serve --port 4096 --hostname 0.0.0.0`
   - `opencode attach` 连接方式（⚠️ 注：官方文档用 `--attach` 参数）

7. **CSDN 问答**（2026-04-01）
   - 标题：如何让 opencode serve 命令在后台长期运行并开机自启？
   - 内容：Linux 用 systemd，Windows 用 NSSM，配置 `Restart=always`
   - 浏览量：45

8. **CSDN 多篇文章**（2026-01 至 2026-05）
   - 标题包含：OpenCode 终极安装教程、3分钟安装指南、完全学习指南
   - 一致引用 `curl -fsSL https://opencode.ai/install | bash`
   - 多篇标注 Stars 突破 5万/10万

9. **博客园**（2026-04-08）
   - OpenCode 详细攻略，开源版 Claude Code
   - 四种形态：命令行 / 桌面客户端 / 插件 / 云端
   - 免费模型使用说明

10. **知乎**（2026-01-12）
    - opencode又爆火了，难道原因是开源?
    - Stars 6.4万，增长轨迹记录

11. **腾讯新闻**（2026-03-01）
    - Open Cowork来了，支持远程操控和通用GUI操作
    - 独立报道 OpenWork 项目（非 OpenCode 官方）

### 四、Skills 系统来源

12. **runoob.com - OpenCode Skills** — https://www.runoob.com/opencode/opencode-skills.html
    - 6 个 Skills 搜索路径详细列表
    - 目录结构示例

13. **opencode-tutorial.com - Agent Skills** — https://opencode-tutorial.com/zh/docs/skills
    - frontmatter 字段规范
    - 发现机制说明

14. **CSDN 多篇**（2026-03 至 2026-04）
    - Skills 核心结构、SKILL.md 格式、渐进式加载机制

### 五、GitHub Stars 增长轨迹

| 日期 | Stars | 来源 |
|------|-------|------|
| 2026-01 初 | ~50k | CSDN devpress |
| 2026-01-12 | 64k | 知乎回答 |
| 2026-02-12 | 103k | 头条 |
| 2026-02-17 | 50k+ | CSDN blog |
| 2026-03-02 | 100k+ | 掘金 |
| 2026-03-07 | 95k+ | CSDN |
| 2026-05-13 | 159k | GitHub 实时 |

---

## 调研方法说明

本次调研采用**多源三角验证法**：

1. **直接访问**：官网、文档、GitHub 实时数据
2. **独立第三方文档**：opencodeguide.com（非官方但权威）
3. **社区多源**：10+ 篇独立博客、CSDN、知乎、博客园、腾讯新闻
4. **GitHub Stars 增长曲线**：跨时间多源数据交叉对比

## 待验证/存疑项

- `opencode attach` vs `opencode run --attach`：极客日志写法与官方文档有出入，以官方 `--attach` 参数为准
- OpenWork 与 OpenCode 的关系：OpenWork 是独立项目，由 different-ai 团队开发，并非 OpenCode 官方产品
