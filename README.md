# LLM Wiki

基于 Karpathy 的 [LLM Wiki](https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md) 模式构建的个人知识库。

## 核心理念

LLM 增量构建和维护一个持久的 wiki——一个位于原始文档和你之间的结构化、互联的 markdown 文件集合。知识被编译一次，然后保持最新，而不是每次查询时重新派生。

```
Raw Sources (不可变) → LLM处理 → Wiki (持久、递增)
                                       ↓
                                 Obsidian 展示
```

## 工具栈

- **OpenCode**: LLM 代理，读取 AGENTS.md 和 SKILLS/ 来维护 wiki
- **Markdown**: 纯文本格式，wiki 的载体
- **Obsidian**: 知识库阅读和展示工具

## 目录结构

```
llm-wiki/
├── AGENTS.md           # Schema 规范 - LLM 的操作手册
├── index.md            # 内容目录 - 快速导航
├── log.md              # 活动日志 - 时间线记录
├── SKILLS/             # 技能定义
│   ├── wiki-maintenance.md
│   ├── source-ingest.md
│   └── index-management.md
├── raw/                # 源文档 (只读)
│   ├── sources.md      # 源文档元数据
│   └── assets/         # 图片/附件
└── wiki/               # LLM 生成的内容
    ├── entities/       # 人物、机构、实体
    ├── concepts/       # 概念、主题、领域
    ├── sources/        # 源文档摘要页
    └── synthesis/      # 综合分析、比较、论文
```

## 工作流

### 1. 摄入源文档 (Ingest)

```
1. 将源文档放入 raw/
2. 在 OpenCode 中告诉 LLM: "处理新的源文档"
3. LLM 按照 source-ingest.md 执行:
   - 创建源文档摘要页 (wiki/sources/)
   - 提取实体并更新实体页 (wiki/entities/)
   - 提取概念并更新概念页 (wiki/concepts/)
   - 更新 index.md
   - 追加 log.md
4. 在 Obsidian 中查看结果
```

**单次摄入可能影响 10-15 个 wiki 页面。**

### 2. 提问 (Query)

```
1. 在 OpenCode 中提问
2. LLM 读取 index.md 找到相关页面
3. 读取相关 wiki 页面
4. 综合回答并引用 [[页面链接]]
5. 好答案可以归档回 wiki 作为新页面
```

### 3. 维护 (Lint)

```
周期性告诉 LLM: "检查 wiki 健康状态"
LLM 会检查:
- 页面间矛盾
- 过时信息
- 孤立页面 (无入链)
- 缺失的交叉引用
- 可填充的数据空白
```

## Obsidian 使用

### 推荐设置

1. **打开 vault**: 指向 `llm-wiki/` 目录
2. **附件文件夹**: 设置为 `raw/assets/`
3. **绑定热键**: "下载附件" → `Ctrl+Shift+D`
4. **建议安装插件**:
   - **Dataview**: 通过 frontmatter 查询页面
   - **Marp**: 幻灯片支持
   - **Obsidian Git**: 版本控制

### 日常使用

```
┌─────────────────────┐    ┌─────────────────────┐
│     OpenCode        │    │      Obsidian       │
│                     │    │                     │
│  LLM 做所有编辑     │ ←→ │  实时浏览结果       │
│  - 写入 wiki        │    │  - 图形视图         │
│  - 更新交叉引用     │    │  - 双向链接         │
│  - 维护 index/log   │    │  - 搜索             │
└─────────────────────┘    └─────────────────────┘
```

## Wiki 页面格式

每个页面必须包含 YAML frontmatter:

```yaml
---
title: "页面标题"
type: entity|concept|source|synthesis
created: 2026-04-30
updated: 2026-04-30
tags: [tag1, tag2]
sources: [相关源文档]
related: [相关页面]
---

# 页面标题

内容...
```

## 快速开始

1. 将 `llm-wiki/` 目录作为 vault 打开于 Obsidian
2. 在 OpenCode 中打开同一目录
3. 将第一篇源文档放入 `raw/`
4. 告诉 OpenCode: "处理这个源文档"
5. 在 Obsidian 中查看生成的 wiki 页面

## 解析日志

查看最近活动:

```bash
grep "^## \[" log.md | tail -5    # 最近5条
grep "^## .*ingest" log.md        # 所有摄入记录
grep "^## .*lint" log.md          # 所有维护记录
```

## 约定

- 页面名: 小写、中划线分隔
- 链接: `[[category/page-name]]`
- 日期: `YYYY-MM-DD` 格式
- 类型: `entity`, `concept`, `source`, `synthesis` 之一