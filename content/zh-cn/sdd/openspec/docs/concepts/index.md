---
title: "概念"
linkTitle: "概念"
weight: 60
date: 2026-02-13
description: >
  OpenSpec 概念
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/concepts.md

（Gemini 机翻）

----

本指南解释了 OpenSpec 背后的核心思想以及它们如何协同工作。

## 哲学

OpenSpec 建立在四个原则之上：

```
fluid not rigid         — 流动而非僵化：没有阶段性关卡，专注于有意义的工作
iterative not waterfall — 迭代而非瀑布：在构建中学习，在进行中优化
easy not complex        — 简单而非复杂：轻量级设置，最少的仪式感
brownfield-first        — 棕地优先：适用于现有代码库，而不仅仅是新项目
```

### 为什么这些原则很重要

**流动而非僵化 (Fluid not rigid)。** 传统的规范系统将你锁定在固定的阶段中：先计划，再实现，最后完成。OpenSpec 更加灵活——你可以按任何对工作有意义的顺序创建工件 (artifacts)。

**迭代而非瀑布 (Iterative not waterfall)。** 需求会变。理解会加深。起初看起来不错的方法，在接触代码库后可能就不再适用了。OpenSpec 拥抱这一现实。

**简单而非复杂 (Easy not complex)。** 一些规范框架需要广泛的设置、严格的格式或繁重的流程。OpenSpec 不会阻碍你。几秒钟内初始化，立即开始工作，只有在需要时才进行自定义。

**棕地优先 (Brownfield-first)。** 大多数软件工作不是从零开始构建，而是修改现有的系统。OpenSpec 基于增量 (delta-based) 的方法使得指定现有行为的变更变得容易，而不仅仅是描述新系统。

## 宏观图景

OpenSpec 将你的工作组织成两个主要区域：

```
┌─────────────────────────────────────────────────────────────────┐
│                        openspec/                                 │
│                                                                  │
│   ┌─────────────────────┐      ┌──────────────────────────────┐ │
│   │       specs/        │      │         changes/              │ │
│   │                     │      │                               │ │
│   │  事实来源 (Truth)    │◄─────│  提议的修改 (Proposals)       │ │
│   │  系统当前的工作方式   │ merge│  每个变更 = 一个文件夹        │ │
│   │                     │      │  包含工件 + 增量 (deltas)     │ │
│   │                     │      │                               │ │
│   └─────────────────────┘      └──────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Specs (规范)** 是事实来源 (source of truth) —— 它们描述了系统当前的行为方式。

**Changes (变更)** 是提议的修改 —— 它们存在于独立的文件夹中，直到你准备好合并它们。

这种分离是关键。你可以并行处理多个变更而不会发生冲突。你可以在变更影响主规范之前对其进行审查。当你归档 (archive) 一个变更时，它的增量会干净地合并到事实来源中。

## 规范 (Specs)

规范使用结构化的需求 (requirements) 和场景 (scenarios) 来描述系统的行为。

### 结构

```
openspec/specs/
├── auth/
│   └── spec.md           # 认证行为
├── payments/
│   └── spec.md           # 支付处理
├── notifications/
│   └── spec.md           # 通知系统
└── ui/
    └── spec.md           # UI 行为和主题
```

按领域 (domain) 组织规范 —— 对系统有意义的逻辑分组。常见的模式：

- **按功能区域**: `auth/`, `payments/`, `search/`
- **按组件**: `api/`, `frontend/`, `workers/`
- **按限界上下文**: `ordering/`, `fulfillment/`, `inventory/`

### 规范格式

规范包含需求，每个需求都有场景：

```markdown
# Auth Specification

## Purpose
应用程序的认证和会话管理。

## Requirements

### Requirement: User Authentication
系统 SHALL (必须) 在登录成功后签发 JWT 令牌。

#### Scenario: Valid credentials
- GIVEN a user with valid credentials (给定一个拥有有效凭证的用户)
- WHEN the user submits login form (当用户提交登录表单时)
- THEN a JWT token is returned (那么返回一个 JWT 令牌)
- AND the user is redirected to dashboard (并且用户被重定向到仪表盘)

#### Scenario: Invalid credentials
- GIVEN invalid credentials (给定无效的凭证)
- WHEN the user submits login form (当用户提交登录表单时)
- THEN an error message is displayed (那么显示错误消息)
- AND no token is issued (并且不签发令牌)

### Requirement: Session Expiration
系统 MUST (必须) 在 30 分钟不活动后使会话过期。

#### Scenario: Idle timeout
- GIVEN an authenticated session (给定一个已认证的会话)
- WHEN 30 minutes pass without activity (当 30 分钟无活动过去时)
- THEN the session is invalidated (那么会话失效)
- AND the user must re-authenticate (并且用户必须重新认证)
```

**关键要素:**

| 要素 | 用途 |
|---------|---------|
| `## Purpose` | 该规范领域的层级描述 |
| `### Requirement:` | 系统必须具备的特定行为 |
| `#### Scenario:` | 需求运作的具体示例 |
| SHALL/MUST/SHOULD | RFC 2119 关键字，指示需求强度 |

### 为什么要这样组织规范

**需求是 "What" (做什么)** —— 它们陈述系统应该做什么，而不指定实现方式。

**场景是 "When" (何时/场景)** —— 它们提供可验证的具体示例。好的场景：
- 可测试（你可以为它们编写自动化测试）
- 涵盖快乐路径 (happy path) 和边缘情况
- 使用 Given/When/Then 或类似的结构化格式

**RFC 2119 关键字** (SHALL, MUST, SHOULD, MAY) 传达意图：
- **MUST/SHALL** — 绝对要求
- **SHOULD** — 推荐，但存在例外
- **MAY** — 可选

## 变更 (Changes)

变更是对系统提议的修改，打包为一个文件夹，其中包含理解和实现它所需的一切。

### 变更结构

```
openspec/changes/add-dark-mode/
├── proposal.md           # 原因和内容 (Why and What)
├── design.md             # 方式 (技术方案 How)
├── tasks.md              # 实现清单
├── .openspec.yaml        # 变更元数据（可选）
└── specs/                # 增量规范 (Delta specs)
    └── ui/
        └── spec.md       # ui/spec.md 中发生的变化
```

每个变更都是自包含的。它拥有：
- **工件 (Artifacts)** — 捕捉意图、设计和任务的文档
- **增量规范 (Delta specs)** — 针对新增、修改或移除内容的规范
- **元数据 (Metadata)** — 此特定变更的可选配置

### 为什么变更表现为文件夹

将变更打包为文件夹有几个好处：

1. **聚合所有内容。** 提案、设计、任务和规范都在一个地方。无需在不同位置寻找。

2. **并行工作。** 多个变更可以同时存在而不会冲突。在进行 `fix-auth-bug` 的同时也可以处理 `add-dark-mode`。

3. **清晰的历史记录。** 归档后，变更会带着其完整的上下文移动到 `changes/archive/`。你可以回顾并理解不仅仅是变了什么，还有为什么变。

4. **易于审查。** 变更文件夹易于审查——打开它，阅读提案，检查设计，查看规范增量。

## 工件 (Artifacts)

工件是变更中指导工作的文档。

### 工件流

```
proposal ──────► specs ──────► design ──────► tasks ──────► implement
    │               │             │              │
   why            what           how          steps
 + scope        changes       approach      to take
 (原因+范围)      (变了什么)     (方法/路径)    (执行步骤)
```

工件相互构建。每个工件都为下一个工件提供上下文。

### 工件类型

#### 提案 (`proposal.md`)

提案在高层次上捕捉 **意图 (intent)**、**范围 (scope)** 和 **方法 (approach)**。

```markdown
# Proposal: Add Dark Mode

## Intent
用户请求增加暗黑模式选项，以减少夜间使用时的眼睛疲劳
并匹配系统偏好设置。

## Scope
范围内 (In scope):
- 设置中的主题切换开关
- 系统偏好检测
- 在 localStorage 中持久化偏好

范围外 (Out of scope):
- 自定义颜色主题（未来工作）
- 逐页主题覆盖

## Approach
使用 CSS 自定义属性进行主题化，并使用 React Context 
进行状态管理。在首次加载时检测系统偏好，
允许手动覆盖。
```

**何时更新提案:**
- 范围变更（缩小或扩大）
- 意图变得更清晰（对问题的理解加深）
- 方法发生根本性转变

#### 规范 (位于 `specs/` 中的增量规范)

增量规范描述了相对于当前规范 **正在发生什么变化**。请参阅下方的 [增量规范](#delta-specs)。

#### 设计 (`design.md`)

设计捕捉 **技术方案** 和 **架构决策**。

```markdown
# Design: Add Dark Mode

## Technical Approach
主题状态通过 React Context 管理以避免 Prop 逐层传递 (prop drilling)。
CSS 自定义属性支持运行时切换，无需类名切换。

## Architecture Decisions

### Decision: Context over Redux
使用 React Context 处理主题状态，因为：
- 简单的二进制状态 (亮/暗)
- 没有复杂的状态转换
- 避免增加 Redux 依赖

### Decision: CSS Custom Properties
使用 CSS 变量而不是 CSS-in-JS，因为：
- 适用于现有的样式表
- 无运行时开销
- 浏览器原生解决方案

## Data Flow
```
ThemeProvider (context)
       │
       ▼
ThemeToggle ◄──► localStorage
       │
       ▼
CSS Variables (applied to :root)
```

## File Changes
- `src/contexts/ThemeContext.tsx` (new)
- `src/components/ThemeToggle.tsx` (new)
- `src/styles/globals.css` (modified)
```

**何时更新设计:**
- 实现过程中发现方案行不通
- 发现了更好的解决方案
- 依赖关系或约束条件发生变化

#### 任务 (`tasks.md`)

任务是 **实现清单** —— 带有复选框的具体步骤。

```markdown
# Tasks

## 1. Theme Infrastructure
- [ ] 1.1 创建带有亮/暗状态的 ThemeContext
- [ ] 1.2 添加用于颜色的 CSS 自定义属性
- [ ] 1.3 实现 localStorage 持久化
- [ ] 1.4 添加系统偏好检测

## 2. UI Components
- [ ] 2.1 创建 ThemeToggle 组件
- [ ] 2.2 在设置页面添加切换开关
- [ ] 2.3 更新 Header 以包含快速切换

## 3. Styling
- [ ] 3.1 定义暗黑主题调色板
- [ ] 3.2 更新组件以使用 CSS 变量
- [ ] 3.3 测试可访问性的对比度
```

**任务最佳实践:**
- 将相关任务分组在标题下
- 使用层级编号 (1.1, 1.2 等)
- 保持任务足够小，以便在一次会话中完成
- 完成任务后勾选它们

## 增量规范 (Delta Specs)

增量规范是使 OpenSpec 适用于棕地 (brownfield) 开发的关键概念。它们描述 **发生了什么变化**，而不是重述整个规范。

### 格式

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
系统 MUST (必须) 支持基于 TOTP 的双因素认证。

#### Scenario: 2FA enrollment
- GIVEN a user without 2FA enabled
- WHEN the user enables 2FA in settings
- THEN a QR code is displayed for authenticator app setup
- AND the user must verify with a code before activation

#### Scenario: 2FA login
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented
- AND login completes only after valid OTP

## MODIFIED Requirements

### Requirement: Session Expiration
系统 MUST (必须) 在 15 分钟不活动后使会话过期。
(Previously: 30 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 15 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements

### Requirement: Remember Me
(已弃用，以此支持 2FA。用户应在每次会话时重新认证。)
```

### 增量部分

| 部分 | 含义 | 归档时发生什么 |
|---------|---------|------------------------|
| `## ADDED Requirements` | 新增行为 | 追加到主规范 |
| `## MODIFIED Requirements` | 变更行为 | 替换现有需求 |
| `## REMOVED Requirements` | 弃用行为 | 从主规范中删除 |

### 为什么使用增量而不是全量规范

**清晰度。** 增量准确显示了变化的内容。阅读全量规范时，你需要在大脑中将其与当前版本进行对比。

**避免冲突。** 只要两个变更修改的是不同的需求，它们就可以触及同一个规范文件而不会发生冲突。

**审查效率。** 审查者看到的是变更，而不是未变更的上下文。专注于重要的事情。

**适应棕地项目。** 大多数工作都是修改现有的行为。增量使“修改”成为一等公民，而不是事后的补充。

## 模式 (Schemas)

模式定义了工作流的工件类型及其依赖关系。

### 模式如何工作

```yaml
# openspec/schemas/spec-driven/schema.yaml
name: spec-driven
artifacts:
  - id: proposal
    generates: proposal.md
    requires: []              # 无依赖，可首先创建

  - id: specs
    generates: specs/**/*.md
    requires: [proposal]      # 创建前需要 proposal

  - id: design
    generates: design.md
    requires: [proposal]      # 可与 specs 并行创建

  - id: tasks
    generates: tasks.md
    requires: [specs, design] # 需要先完成 specs 和 design
```

**工件形成依赖图:**

```
                    proposal
                   (根节点)
                       │
         ┌─────────────┴─────────────┐
         │                           │
         ▼                           ▼
      specs                       design
   (requires:                  (requires:
    proposal)                   proposal)
         │                           │
         └─────────────┬─────────────┘
                       │
                       ▼
                    tasks
                (requires:
                specs, design)
```

**依赖是推动者，而非关卡。** 它们展示了接下来 *可能* 创建什么，而不是 *必须* 创建什么。如果你不需要设计，你可以跳过它。你可以在设计之前或之后创建规范——两者都只依赖于提案。

### 内置模式

**spec-driven** (默认)

规范驱动开发的标准工作流：

```
proposal → specs → design → tasks → implement
```

最适用于：大多数功能开发工作，你希望在实现之前对规范达成一致。

### 自定义模式

为你的团队工作流创建自定义模式：

```bash
# 从头创建
openspec schema init research-first

# 或者 Fork 现有的模式
openspec schema fork spec-driven research-first
```

**自定义模式示例:**

```yaml
# openspec/schemas/research-first/schema.yaml
name: research-first
artifacts:
  - id: research
    generates: research.md
    requires: []           # 首先做研究

  - id: proposal
    generates: proposal.md
    requires: [research]   # 提案基于研究

  - id: tasks
    generates: tasks.md
    requires: [proposal]   # 跳过 specs/design，直接进入 tasks
```

有关创建和使用自定义模式的完整详细信息，请参阅 [自定义](customization.md)。

## 归档 (Archive)

归档通过将其增量规范合并到主规范并保存历史记录来完成一个变更。

### 归档时发生什么

```
归档前 (Before archive):

openspec/
├── specs/
│   └── auth/
│       └── spec.md ◄────────────────┐
└── changes/                         │
    └── add-2fa/                     │
        ├── proposal.md              │
        ├── design.md                │ merge
        ├── tasks.md                 │
        └── specs/                   │
            └── auth/                │
                └── spec.md ─────────┘


归档后 (After archive):

openspec/
├── specs/
│   └── auth/
│       └── spec.md        # 现在包含 2FA 需求
└── changes/
    └── archive/
        └── 2025-01-24-add-2fa/    # 保留用于历史记录
            ├── proposal.md
            ├── design.md
            ├── tasks.md
            └── specs/
                └── auth/
                    └── spec.md
```

### 归档过程

1. **合并增量。** 每个增量规范部分 (ADDED/MODIFIED/REMOVED) 都应用于相应的主规范。

2. **移动到归档。** 变更文件夹移动到 `changes/archive/`，并带有日期前缀以便按时间排序。

3. **保留上下文。** 所有工件在归档中保持完整。你总是可以回头查看，以了解为什么要进行更改。

### 为什么归档很重要

**整洁的状态。** 活跃变更目录 (`changes/`) 仅显示正在进行的工作。已完成的工作已被移走。

**审计轨迹。** 归档保留了每个变更的完整上下文——不仅是变了什么，还包括解释“为什么”的提案、解释“如何做”的设计，以及展示已完成工作的任务。

**规范演进。** 随着变更被归档，规范有机地增长。每个归档都会合并其增量，随着时间的推移建立起全面的规范。

## 整体协作流程

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            OPENSPEC 流程                                     │
│                                                                              │
│   ┌────────────────┐                                                         │
│   │  1. 开始变更   │  /opsx:new 创建一个变更文件夹                           │
│   │  (START CHANGE)│                                                         │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  2. 创建工件   │  /opsx:ff 或 /opsx:continue                             │
│   │(CREATE ARTIF.) │  创建 proposal → specs → design → tasks                 │
│   │                │  (基于模式依赖关系)                                     │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  3. 实现任务   │  /opsx:apply                                            │
│   │(IMPL. TASKS)   │  处理任务，逐项勾选                                     │
│   │                │◄──── 随着学习深入更新工件                               │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  4. 验证工作   │  /opsx:verify (可选)                                    │
│   │(VERIFY WORK)   │  检查实现是否符合规范                                   │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐     ┌──────────────────────────────────────────────┐   │
│   │  5. 归档变更   │────►│  增量规范合并入主规范                         │   │
│   │(ARCHIVE CHANGE)│     │  变更文件夹移动到 archive/                    │   │
│   └────────────────┘     │  规范现在是更新后的事实来源                   │   │
│                          └──────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

**良性循环:**

1. 规范描述当前行为
2. 变更提议修改（作为增量）
3. 实现使变更成为现实
4. 归档将增量合并到规范中
5. 规范现在描述新的行为
6. 下一个变更建立在更新后的规范之上

## 术语表 (Glossary)

| 术语 | 定义 |
|------|------------|
| **Artifact (工件)** | 变更内的文档 (proposal, design, tasks, 或 delta specs) |
| **Archive (归档)** | 完成变更并将其增量合并到主规范的过程 |
| **Change (变更)** | 对系统的提议修改，打包为包含工件的文件夹 |
| **Delta spec (增量规范)** | 描述相对于当前规范的变化 (ADDED/MODIFIED/REMOVED) 的规范 |
| **Domain (领域)** | 规范的逻辑分组 (例如 `auth/`, `payments/`) |
| **Requirement (需求)** | 系统必须具备的特定行为 |
| **Scenario (场景)** | 需求的具体示例，通常采用 Given/When/Then 格式 |
| **Schema (模式)** | 工件类型及其依赖关系的定义 |
| **Spec (规范)** | 描述系统行为的规范，包含需求和场景 |
| **Source of truth (事实来源)** | `openspec/specs/` 目录，包含当前达成一致的行为 |
