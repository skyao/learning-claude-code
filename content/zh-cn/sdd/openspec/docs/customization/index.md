---
title: "自定义"
linkTitle: "自定义"
weight: 80
date: 2026-02-13
description: >
  OpenSpec 自定义
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/customization.md

（Gemini 机翻）

----

OpenSpec 提供三个层级的自定义功能：

| 层级 | 功能 | 适用场景 |
|-------|--------------|----------|
| **项目配置 (Project Config)** | 设置默认值，注入上下文/规则 | 大多数团队 |
| **自定义模式 (Custom Schemas)** | 定义您自己的工作流工件 | 拥有独特流程的团队 |
| **全局覆盖 (Global Overrides)** | 在所有项目中共享模式 | 高级用户 |

---

## 项目配置

`openspec/config.yaml` 文件是为团队自定义 OpenSpec 最简单的方法。它允许您：

- **设置默认模式** - 在执行命令时跳过 `--schema` 参数
- **注入项目上下文** - 让 AI 了解您的技术栈、约定等
- **添加单工件规则** - 针对特定工件的自定义规则

### 快速设置

```bash
openspec init
```

该命令将引导您交互式地创建配置文件。或者，您可以手动创建一个：

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js, PostgreSQL
  API style: RESTful, documented in docs/api.md
  Testing: Jest + React Testing Library
  We value backwards compatibility for all public APIs
  (技术栈：TypeScript, React, Node.js, PostgreSQL
  API 风格：RESTful，记录在 docs/api.md 中
  测试：Jest + React Testing Library
  我们重视所有公共 API 的向后兼容性)

rules:
  proposal:
    - Include rollback plan (包含回滚计划)
    - Identify affected teams (识别受影响的团队)
  specs:
    - Use Given/When/Then format (使用 Given/When/Then 格式)
    - Reference existing patterns before inventing new ones (在发明新模式前先参考现有模式)
```

### 工作原理

**默认模式 (Default schema):**

```bash
# 未配置时
openspec new change my-feature --schema spec-driven

# 配置后 - 自动使用指定模式
openspec new change my-feature
```

**上下文和规则注入:**

当生成任何工件时，您的上下文和规则会被注入到 AI 提示词 (prompt) 中：

```xml
<context>
Tech stack: TypeScript, React, Node.js, PostgreSQL
...
</context>

<rules>
- Include rollback plan
- Identify affected teams
</rules>

<template>
[Schema's built-in template] (模式的内置模板)
</template>
```

- **上下文 (Context)** 出现在**所有**工件中
- **规则 (Rules)** **仅**出现在匹配的工件中

### 模式解析顺序

当 OpenSpec 需要模式时，它会按以下顺序进行检查：

1. CLI 标志：`--schema <name>`
2. 变更元数据（变更文件夹中的 `.openspec.yaml`）
3. 项目配置 (`openspec/config.yaml`)
4. 默认值 (`spec-driven`)

---

## 自定义模式 (Custom Schemas)

当项目配置不足以满足需求时，您可以创建一个拥有完全自定义工作流的模式。自定义模式位于项目的 `openspec/schemas/` 目录中，并随代码一起进行版本控制。

```text
your-project/
├── openspec/
│   ├── config.yaml        # 项目配置
│   ├── schemas/           # 自定义模式放在这里
│   │   └── my-workflow/
│   │       ├── schema.yaml
│   │       └── templates/
│   └── changes/           # 您的变更
└── src/
```

### 复刻 (Fork) 现有模式

自定义的最快方法是复刻一个内置模式：

```bash
openspec schema fork spec-driven my-workflow
```

这会将整个 `spec-driven` 模式复制到 `openspec/schemas/my-workflow/`，您可以自由编辑它。

**您将获得：**

```text
openspec/schemas/my-workflow/
├── schema.yaml           # 工作流定义
└── templates/
    ├── proposal.md       # 提案工件的模板
    ├── spec.md           # 规范模板
    ├── design.md         # 设计模板
    └── tasks.md          # 任务模板
```

现在，编辑 `schema.yaml` 以更改工作流，或编辑模板以更改 AI 生成的内容。

### 从头创建模式

要创建一个全新的工作流：

```bash
# 交互式
openspec schema init research-first

# 非交互式
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

### 模式结构

模式定义了工作流中的工件以及它们之间的依赖关系：

```yaml
# openspec/schemas/my-workflow/schema.yaml
name: my-workflow
version: 1
description: My team's custom workflow (我团队的自定义工作流)

artifacts:
  - id: proposal
    generates: proposal.md
    description: Initial proposal document (初始提案文档)
    template: proposal.md
    instruction: |
      Create a proposal that explains WHY this change is needed.
      Focus on the problem, not the solution.
      (创建一个提案来解释为什么需要这个变更。
      专注于问题，而不是解决方案。)
    requires: []

  - id: design
    generates: design.md
    description: Technical design (技术设计)
    template: design.md
    instruction: |
      Create a design document explaining HOW to implement.
      (创建一个设计文档来解释如何实现。)
    requires:
      - proposal    # 在提案存在之前无法创建设计

  - id: tasks
    generates: tasks.md
    description: Implementation checklist (实现清单)
    template: tasks.md
    requires:
      - design

apply:
  requires: [tasks]
  tracks: tasks.md
```

**关键字段：**

| 字段 | 用途 |
|-------|---------|
| `id` | 唯一标识符，用于命令和规则 |
| `generates` | 输出文件名（支持 Glob 模式，如 `specs/**/*.md`） |
| `template` | `templates/` 目录下的模板文件 |
| `instruction` | 用于创建此工件的 AI 指令 |
| `requires` | 依赖关系 - 哪些工件必须先存在 |

### 模板

模板是指导 AI 的 Markdown 文件。在创建相应工件时，它们会被注入到提示词中。

```markdown
<!-- templates/proposal.md -->
## Why

<!-- Explain the motivation for this change. What problem does this solve? -->

## What Changes

<!-- Describe what will change. Be specific about new capabilities or modifications. -->

## Impact

<!-- Affected code, APIs, dependencies, systems -->
```

模板可以包含：
- AI 应该填充的章节标题
- 带有指导 AI 说明的 HTML 注释
- 展示预期结构的示例格式

### 验证您的模式

在使用自定义模式之前，请对其进行验证：

```bash
openspec schema validate my-workflow
```

这将检查：
- `schema.yaml` 语法是否正确
- 所有引用的模板是否存在
- 是否存在循环依赖
- 工件 ID 是否有效

### 使用您的自定义模式

创建完成后，通过以下方式使用您的模式：

```bash
# 在命令中指定
openspec new change feature --schema my-workflow

# 或者在 config.yaml 中设置为默认
schema: my-workflow
```

### 调试模式解析

不确定正在使用哪个模式？使用以下命令检查：

```bash
# 查看特定模式从何处解析
openspec schema which my-workflow

# 列出所有可用模式
openspec schema which --all
```

输出将显示它来自您的项目、用户目录还是软件包：

```text
Schema: my-workflow
Source: project
Path: /path/to/project/openspec/schemas/my-workflow
```

---

> **注意：** OpenSpec 也支持位于 `~/.local/share/openspec/schemas/` 的用户级模式，以便在不同项目间共享，但推荐使用 `openspec/schemas/` 中的项目级模式，因为它们会随代码一起进行版本控制。

---

## 示例

### 快速迭代工作流

一个用于快速迭代的最小化工作流：

```yaml
# openspec/schemas/rapid/schema.yaml
name: rapid
version: 1
description: Fast iteration with minimal overhead

artifacts:
  - id: proposal
    generates: proposal.md
    description: Quick proposal
    template: proposal.md
    instruction: |
      Create a brief proposal for this change.
      Focus on what and why, skip detailed specs.
    requires: []

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires: [proposal]

apply:
  requires: [tasks]
  tracks: tasks.md
```

### 添加审查工件

复刻默认模式并添加一个审查步骤：

```bash
openspec schema fork spec-driven with-review
```

然后编辑 `schema.yaml` 添加以下内容：

```yaml
  - id: review
    generates: review.md
    description: Pre-implementation review checklist (实现前审查清单)
    template: review.md
    instruction: |
      Create a review checklist based on the design.
      Include security, performance, and testing considerations.
    requires:
      - design

  - id: tasks
    # ... existing tasks config ...
    requires:
      - specs
      - design
      - review    # 现在任务也需要依赖审查
```

---

