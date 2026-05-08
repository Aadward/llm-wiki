---
title: Hermes Agent SOUL.md / AGENTS.md 深度定制
created: 2026-05-09
updated: 2026-05-09
type: concept
tags: [agent, hermes, soul, agents, personality, system-prompt, context]
sources: [web/cloud.tencent.com/developer/article/2655361, web/segmentfault.com/a/1190000047715505]
confidence: high
---

# Hermes Agent SOUL.md / AGENTS.md 深度定制

## 概述

SOUL.md 和 AGENTS.md 是 Hermes Agent 的**上下文注入文件**，用于定义 Agent 的身份、人格、行为规范和项目级指令。它们在系统提示词中按优先级顺序排列，共同塑造 Agent 的"灵魂"。

---

## 一、文件体系总览

```
~/.hermes/                          # 配置根目录
├── config.yaml                     # 主配置文件
├── .env                           # API Keys
├── SOUL.md                        # ★ Agent 人格定义（优先级最高）
├── AGENTS.md                      # ★ Agent 行为指令（项目级）
├── memories/
│   ├── MEMORY.md                  # 环境记忆（约 2,200 字符上限）
│   └── USER.md                    # 用户画像（约 1,375 字符上限）
└── skills/                        # 技能库
```

---

## 二、SOUL.md：Agent 人格定义

### 2.1 定位与作用

SOUL.md 是**系统提示词的第一位**，定义 Agent 的核心人格、沟通风格和专业领域。它直接影响模型对所有问题的响应方式。

### 2.2 内容结构

```markdown
---
title: My Hermes Assistant
description: A helpful AI assistant specialized in Python development
version: 1.0
personality:
  tone: professional_but_friendly
  communication_style: concise
  expertise:
    - Python
    - Data Science
    - Terminal automation
  languages:
    primary: Chinese
    secondary: English
---

# My Agent's Soul

## 核心人格

你是一位经验丰富、严谨但不失友好的 Python 开发专家。
你习惯用最简洁的代码解决问题，不喜欢过度设计。
遇到不确定的问题，你会直接承认，而不是编造答案。

## 沟通风格

- 回答尽量简洁，直接给出解决方案
- 需要时提供代码示例
- 遇到复杂问题先问清楚需求再动手
- 重要决策给出理由

## 专业边界

- 擅长：Python 开发、数据处理、Shell 脚本、API 设计
- 不擅长：UI 设计、游戏开发、移动端原生开发
- 不做：危险命令执行（rm -rf / 等）、支付相关操作
```

### 2.3 personality 字段说明

| 字段 | 可选值 | 说明 |
|------|--------|------|
| `tone` | `professional`, `friendly`, `casual`, `formal` | 基本语调 |
| `communication_style` | `concise`, `detailed`, `balanced` | 沟通风格 |
| `expertise` | 字符串列表 | 专业领域（影响工具推荐优先级） |
| `languages` | `primary`, `secondary` | 主要和次要语言 |

### 2.4 最佳实践

- **保持简洁**：SOUL.md 不宜过长，建议控制在 500 字以内
- **明确边界**：写清楚"能做什么"和"不能做什么"
- **避免矛盾**：不要与 config.yaml 中的设置冲突
- **风格一致**：与 USER.md 的用户偏好保持协调

---

## 三、AGENTS.md：Agent 行为指令

### 3.1 定位与作用

AGENTS.md 定义 Agent 在**特定项目或场景**下的行为规范、工具使用限制和工作流程指令。它的作用域是项目级的。

### 3.2 与 SOUL.md 的区别

| 维度 | SOUL.md | AGENTS.md |
|------|---------|-----------|
| **优先级** | 最高（第一位） | 次高 |
| **作用域** | 全局，所有会话 | 项目级/当前目录 |
| **内容** | 人格、风格、边界 | 工作流、工具限制、项目上下文 |
| **位置** | `~/.hermes/SOUL.md` | 项目目录 `AGENTS.md` |

### 3.3 内容结构

```markdown
# My Project Agent Instructions

## 项目背景

这是一个 Next.js 14 + TypeScript + Tailwind CSS 的电商后台项目。
团队使用 GitHub Flow 工作流。

## 工具使用规范

### 允许的操作
- 读取和修改 `/src` 目录下的代码
- 运行 `npm run dev`、`npm run build`、`npm run test`
- 创建不超过 3 个文件的小改动

### 禁止的操作
- 不要直接修改 `/src/styles/globals.css`，使用 Tailwind 类
- 不要提交代码（需要人工 review）
- 不要运行 `npm install` 添加新依赖（需确认）

## 代码风格

- 使用 TypeScript strict mode
- 组件文件使用 PascalCase（如 `UserProfile.tsx`）
- 工具函数使用 camelCase（如 `formatDate.ts`）
- 优先使用 React Hooks，不用 class 组件

## 工作流程

1. 收到需求后，先理解需求再动手
2. 改动前确认文件位置
3. 完成后简述改动内容
4. 重要决策（如架构变更）先提方案
```

### 3.3 优先级规则

```
AGENTS.md（项目目录）> SOUL.md（全局）> config.yaml > 内置默认
```

---

## 四、上下文文件加载机制

### 4.1 加载顺序（`_build_system_prompt()`）

```python
def _build_system_prompt(self) -> str:
    parts = []

    # 1. SOUL.md（人格定义，最高优先级）
    if os.path.exists("SOUL.md"):
        parts.append(self._read_file("SOUL.md"))

    # 2. AGENTS.md（项目级指令）
    if not self.skip_context_files and os.path.exists("AGENTS.md"):
        parts.append(self._read_file("AGENTS.md"))

    # 3. CLAUDE.md（项目上下文，在项目目录）
    if not self.skip_context_files:
        parts.extend(self._load_context_files())

    # 4. 内置系统提示词
    parts.append(self._get_base_system_prompt())

    # 5. Skills Index（最低优先级）
    parts.append(self._get_skills_index())

    return "\n\n".join(parts)
```

### 4.2 跳过机制

```bash
# 跳过上下文文件加载（快速测试用）
hermes chat --skip-context-files

# 跳过记忆检索（调试用）
hermes chat --skip-memory
```

---

## 五、MEMORY.md 和 USER.md

### 5.1 MEMORY.md（环境记忆）

- **作用**：存储 Agent 自动提炼的项目笔记、环境偏好、工作流规律
- **上限**：约 2,200 字符
- **特点**：Agent **自动维护**，不需要手动告诉它记什么
- **内容示例**：
  - 项目使用的技术栈
  - 环境配置偏好
  - 常见工作流程规律
  - 以往决策的历史背景

### 5.2 USER.md（用户画像）

- **作用**：Agent 自动维护的用户画像
- **上限**：约 1,375 字符
- **内容示例**：
  - 用户角色（开发者、运营、管理等）
  - 技术栈偏好
  - 沟通偏好（语言、详细程度）
  - 目标方向

### 5.3 自动注入机制

这两套记忆文件在**每次会话启动时自动注入上下文**，所以用户不需要每次重复说明自己的偏好。

---

## 六、个性化配置进阶

### 6.1 多角色切换

通过 `personalities` 配置多个角色：

```yaml
# config.yaml
personalities:
  - name: developer
    description: 开发专家模式
    soul_file: ~/.hermes/personalities/developer/SOUL.md

  - name: writer
    description: 写作助手模式
    soul_file: ~/.hermes/personalities/writer/SOUL.md
```

使用时切换：
```
/personality developer
```

### 6.2 团队共享配置

```yaml
# ~/.hermes/config.yaml
shared:
  soul: /etc/hermes/shared/SOUL.md  # 团队统一人格
  agents_template: /etc/hermes/templates/AGENTS.md.template
```

### 6.3 按目录定制

在项目目录放置 `CLAUDE.md` 或 `AGENTS.md`：

```
~/project-a/AGENTS.md   # 项目 A 的指令
~/project-b/CLAUDE.md   # 项目 B 的上下文
```

---

## 七、最佳实践 Checklist

### 人格定义（SOUL.md）
- [ ] 明确 Agent 的专业领域和边界
- [ ] 定义沟通风格（简洁/详细）
- [ ] 说明不能做的事（危险操作红线）
- [ ] 控制在 500 字以内

### 行为指令（AGENTS.md）
- [ ] 说明项目背景和技术栈
- [ ] 列出工具使用的允许/禁止项
- [ ] 定义代码风格规范
- [ ] 明确工作流程

### 记忆维护
- [ ] 定期检查 MEMORY.md 内容是否准确
- [ ] USER.md 会自动更新，不需要手动维护
- [ ] 通过 `hermes memory setup` 初始化记忆系统

---

## 相关概念

- [[hermes-agent]] — 整体框架
- [[hermes-agent-source-code-architecture]] — 源码架构（提示词装配）
- [[hermes-agent-memory-architecture]] — 四层记忆架构
- [[hermes-agent-best-practices]] — 最佳实践
