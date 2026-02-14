---
title: "工作流"
linkTitle: "工作流"
weight: 20
date: 2026-02-13
description: >
  OpenSpec 工作流
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/workflows.md

本指南涵盖 OpenSpec 的常见工作流模式及其适用场景。

## 哲学：行动，而非阶段

> Actions, Not Phases

传统的工作流会强制你经历几个阶段：计划、实施、完成。但实际工作并非都能被简单地归入这些框架之中。

OPSX 采取了不同的方法：

```bash
Traditional (phase-locked):

  PLANNING ────────► IMPLEMENTING ────────► DONE
      │                    │
      │   "Can't go back"  │
      └────────────────────┘

OPSX (fluid actions):

  proposal ──► specs ──► design ──► tasks ──► implement
```

关键原则：

- 是行动，而非阶段 ——命令是你可以执行的操作，而不是你被困住的阶段。
- 依赖关系是赋能工具 ——它们展示的是可能性，而不是下一步需要做什么。

## 工作流程模式

### 快速功能

当你清楚自己想要构建什么，只需要执行时：

```bash
/opsx:new ──► /opsx:ff ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

对话示例：

```bash
You: /opsx:new add-logout-button

AI:  Created openspec/changes/add-logout-button/
     Ready to create: proposal

You: /opsx:ff

AI:  Creating all planning artifacts...
     ✓ proposal.md
     ✓ specs/
     ✓ design.md
     ✓ tasks.md
     Ready for implementation!

You: /opsx:apply

AI:  Working through tasks...
     ✓ Added logout button to header
     ✓ Implemented session clearing
     ✓ Added confirmation dialog
     All tasks complete!

You: /opsx:archive

AI:  ✓ Merged specs
     ✓ Archived change
```

最适合： 小型到中型功能、错误修复、简单更改。

### 探索性的

当需求不明确或需要先进行调查时：

```bash
/opsx:explore ──► /opsx:new ──► /opsx:continue ──► ... ──► /opsx:apply
```

对话示例：

```bash
You: /opsx:explore

AI:  What would you like to explore?

You: I want to improve page load performance but I'm not sure
     where the bottlenecks are.

AI:  Let me help investigate...
     [Analyzes bundle sizes, identifies slow queries, reviews
     component render patterns]

     I found three main bottlenecks:
     1. Large unoptimized images
     2. Synchronous data fetching in ProductList
     3. Re-renders from context changes

     Which would you like to address first?

You: Let's tackle the data fetching issue.

You: /opsx:new optimize-product-list-fetching

AI:  Created change. Ready to create: proposal

You: /opsx:continue

AI:  Creating proposal based on our exploration...
     [Writes proposal incorporating investigation insights]

     Ready to create: specs, design
     Which would you like next?

You: /opsx:continue

AI:  Creating specs...
```

最适合： 性能优化、调试、架构决策、需求不明确。

### 并行变更

同时处理多个更改：

```bash
Change A: /opsx:new ──► /opsx:ff ──► /opsx:apply (in progress)
                                         │
                                    context switch
                                         │
Change B: /opsx:new ──► /opsx:ff ──────► /opsx:apply
```

对话示例：

```bash
You: I'm working on add-dark-mode but got pulled into a bug fix

You: /opsx:new fix-login-redirect

AI:  Created openspec/changes/fix-login-redirect/

You: /opsx:ff

AI:  ✓ Created planning artifacts

You: /opsx:apply

AI:  Working on fix-login-redirect...
     [Implements the fix]
     All tasks complete!

You: /opsx:archive

AI:  ✓ Archived fix-login-redirect

You: Let's get back to dark mode

You: /opsx:apply add-dark-mode

AI:  Resuming add-dark-mode...
     Picking up at task 2.3: Update Header...
```

最适合： 并行工作流、紧急中断、团队协作。

当您有多个已完成的更改时，请使用 /opsx:bulk-archive ：

```bash
You: /opsx:bulk-archive

AI:  Found 3 completed changes:
     - add-dark-mode (all tasks done)
     - fix-login-redirect (all tasks done)
     - update-footer (all tasks done)

     Checking for spec conflicts...
     ⚠ add-dark-mode and update-footer both touch specs/ui/

     Inspecting codebase to resolve...
     Both changes are implemented. Will apply in chronological order.

     Archive all 3 changes?

You: Yes

AI:  ✓ Archived add-dark-mode
     ✓ Archived fix-login-redirect
     ✓ Archived update-footer
     Specs merged in order: add-dark-mode → update-footer
```

批量归档功能会检测多个更改何时涉及相同的规范，并通过检查实际实现的内容来解决冲突。

### 完成变更

推荐的完成流程：

```bash
/opsx:apply ──► /opsx:verify ──► /opsx:archive
                    │                 │
              validates          prompts to sync
              implementation     if needed
```

#### 核对：检查工作

/opsx:verify 从三个维度验证您的实现是否符合要求：

```bash
You: /opsx:verify

AI:  Verifying add-auth...

     COMPLETENESS
     ✓ All 12 tasks in tasks.md are checked
     ✓ All requirements in specs have corresponding code
     ⚠ Scenario "Session timeout after inactivity" not tested

     CORRECTNESS
     ✓ Implementation matches spec intent
     ✓ Edge cases from scenarios are handled
     ✓ Error states match spec definitions

     COHERENCE
     ✓ Design decisions reflected in code structure
     ✓ Naming conventions consistent with design.md
     ⚠ Design mentions "event-driven" but implementation uses polling

     SUMMARY
     ─────────────────────────────
     Critical issues: 0
     Warnings: 2
     Ready to archive: Yes (with warnings)

     Recommendations:
     1. Add test for session timeout scenario
     2. Consider refactoring to event-driven as designed, or update design.md
```

验证检查内容：

| 维度                      | 验证内容                                       |
| ------------------------- | ---------------------------------------------- |
| Completeness <br />完整性 | 所有任务已完成，所有需求已实现，所有场景已覆盖 |
| Correctness <br />正确性  | 实现符合规范意图，并处理了各种特殊情况。       |
| Coherence <br />相干性    | 设计决策体现在代码中，模式保持一致             |

验证不会阻止归档，但会发现一些您可能需要先解决的问题。

#### 归档：完成不变更

`/opsx:archive` 完成更改并将其移动到归档：

```bahs
You: /opsx:archive

AI:  Archiving add-auth...

     Artifact status:
     ✓ proposal.md exists
     ✓ specs/ exists
     ✓ design.md exists
     ✓ tasks.md exists (12/12 tasks complete)

     Delta specs: Not yet synced to main
     → Would you like to sync now?

You: Yes

AI:  ✓ Synced specs to openspec/specs/auth/spec.md
     ✓ Moved to openspec/changes/archive/2025-01-24-add-auth/

     Change archived successfully.
```

如果规范未同步，归档程序会发出提示。它不会阻塞未完成的任务，但会发出警告。

## 何时使用什么

`/opsx:ff` 与 `/opsx:continue`

| 情况                               | Use 使用         |
| ---------------------------------- | ---------------- |
| 需求明确，准备开工                 | `/opsx:ff`       |
| 探索过程中，我想回顾每个步骤。     | `/opsx:continue` |
| 希望在制定规范之前对提案进行迭代。 | `/opsx:continue` |
| 时间紧迫，需要快速行动             | `/opsx:ff`       |
| 复杂多变，渴望掌控                 | `/opsx:continue` |

**经验法则：** 如果可以预先描述完整的范围，请使用 `/opsx:ff` 。如果是边做边确定，请使用 `/opsx:continue` 。

### 何时更新，何时全新开始

一个常见的问题是：什么时候可以更新现有更改，什么时候应该开始新的更改？

更新现有更改的情况如下：

- 意图相同，执行更加精细。
- 范围缩小（先做 MVP，其他的稍后再说）
- 基于学习的修正（代码库与预期不符）
- 根据实施过程中发现的问题进行设计调整

在以下情况下启动新的更改：

- 意图发生了根本性改变
- 工作范围迅速扩展到完全不同的领域。
- 原始变更可以单独标记为“已完成”。
- 打补丁只会让人更加困惑，而不是更加清晰。

```bash
                     ┌─────────────────────────────────────┐
                     │     Is this the same work?          │
                     └──────────────┬──────────────────────┘
                                    │
                 ┌──────────────────┼──────────────────┐
                 │                  │                  │
                 ▼                  ▼                  ▼
          Same intent?      >50% overlap?      Can original
          Same problem?     Same scope?        be "done" without
                 │                  │          these changes?
                 │                  │                  │
       ┌────────┴────────┐  ┌──────┴──────┐   ┌───────┴───────┐
       │                 │  │             │   │               │
      YES               NO YES           NO  NO              YES
       │                 │  │             │   │               │
       ▼                 ▼  ▼             ▼   ▼               ▼
    UPDATE            NEW  UPDATE       NEW  UPDATE          NEW
```

**例如：“添加深色模式”**

- “还需要支持自定义主题”→ 新变更（范围扩大）
- “系统偏好检测比预期更难”→ 更新（意图相同）
- “先添加发货开关，稍后再添加偏好设置” → 更新后归档，然后添加新的更改

## 最佳实践

**保持变更的重点**


每次变更都应包含一个逻辑工作单元。例如，如果您要“添加功能 X 并重构 Y”，请考虑将其拆分为两个单独的变更。

重要性：

- 更容易复习和理解
- 更清晰的档案历史记录
- 可独立发货
- 如果需要，回滚操作会更简单

### 为不明确的需求使用 `/opsx:explore` 

在决定做出改变之前，先探索问题领域：

```bash
You: /opsx:explore

AI:  What would you like to explore?

You: How should we handle rate limiting for our API?

AI:  Let me investigate your current setup and options...
     [Researches codebase, considers patterns]

     I see three approaches we could take:
     1. Middleware-based (simple, per-endpoint)
     2. Redis-backed (scalable, shared state)
     3. Token bucket per user (fair, complex)

     Your current stack suggests #1 or #2. What's your scale?
```

探索可以帮助你在创造作品之前理清思路。

### 归档前请核实

使用 `/opsx:verify` 检查实现是否与工件匹配：

```bash
You: /opsx:verify

AI:  Verifying add-dark-mode...

     ✓ All tasks in tasks.md are checked
     ✓ Requirements in specs have corresponding code
     ✓ Design decisions are reflected in implementation

     Ready to archive!
```

在关闭更改之前捕获不匹配项。

### 清晰的命令变更

好的名称能让 `openspec list` 更有用：

```bash
Good:                          Avoid:
add-dark-mode                  feature-1
fix-login-redirect             update
optimize-product-query         changes
implement-2fa                  wip
```

## 命令快速参考

| 命令                 | 目的             | 何时使用                   |
| -------------------- | ---------------- | -------------------------- |
| `/opsx:explore`      | 仔细思考各种想法 | 要求不明确，需调查         |
| `/opsx:new`          | 开始变更         | 开始任何新的工作           |
| `/opsx:continue`     | 创建下一个工件   | 逐步创建制品               |
| `/opsx:ff`           | 创建所有规划文档 | 范围明确，准备开工         |
| `/opsx:apply`        | 执行任务         | 准备编写代码               |
| `/opsx:verify`       | 验证实施         | 归档前，找出不匹配项       |
| `/opsx:sync`         | 合并增量规格     | 可选——根据需要添加存档提示 |
| `/opsx:archive`      | 完成更改         | 所有工作已完成             |
| `/opsx:bulk-archive` | 归档多个更改     | 行工作，批量完成           |



