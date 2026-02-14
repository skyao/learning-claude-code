---
title: "命令"
linkTitle: "命令"
weight: 30
date: 2026-02-13
description: >
  OpenSpec 命令
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/commands.md


这是 OpenSpec 斜杠命令的参考说明。这些命令会在您的 AI 编码助手的聊天界面（例如 Claude Code、Cursor、Windsurf）中调用。

## 快速参考

| 命令                 | 目的                                 |
| -------------------- | ------------------------------------ |
| `/opsx:explore`      | 在决定改变之前，请仔细考虑各种想法。 |
| `/opsx:new`          | 开启新的变革                         |
| `/opsx:continue`     | 根据依赖关系创建下一个工件           |
| `/opsx:ff`           | 快速推进：一次性创建所有规划文档     |
| `/opsx:apply`        | 执行变更中的任务                     |
| `/opsx:verify`       | 验证实现是否与工件相符               |
| `/opsx:sync`         | 将增量规范合并到主规范中             |
| `/opsx:archive`      | 归档已完成的更改                     |
| `/opsx:bulk-archive` | 一次性归档多个更改                   |
| `/opsx:onboard`      | 引导式教程，带您了解完整的工作流     |

------

## 命令参考

### `/opsx:explore`

在决定做出改变之前，要仔细思考想法，调查问题，并明确需求。

语法：

```
/opsx:explore [topic]
```

参数：

| 参数    | 是否必需 | 描述               |
| ------- | -------- | ------------------ |
| `topic` | 不       | 你想探索或调查什么 |

功能：

- 开启一场无需任何结构的探索性对话
- 调查代码库以回答问题
- 比较各种方案和方法
- 创建可视化图表以阐明思路
- 当洞察逐渐清晰时，可以过渡到 `/opsx:new`

例子：

```text
You: /opsx:explore

AI:  What would you like to explore?

You: How should we handle authentication for the mobile app?

AI:  Let me investigate your current auth setup...
     [Analyzes codebase]

     I see you're using session-based auth for the web app.
     For mobile, we have three main options:

     1. JWT tokens (stateless, works offline)
     2. OAuth2 (if you need third-party login)
     3. Extend current sessions (simpler, but requires connectivity)

     Your API already has CORS configured. Which direction interests you?

You: Let's go with JWT. Can we start a change for that?

AI:  Ready when you are. Run /opsx:new add-jwt-auth to begin.
```

Tips: 

- 当需求不明确或需要调查时使用
- 探索过程中不会产生任何制品。
- 便于在做决定前比较多种方法。
- 可以读取文件并搜索代码库

------

### `/opsx:new`

开始新的变更。创建变更文件夹结构，并使用选定的架构搭建框架。

语法：

```bash
/opsx:new [change-name] [--schema <schema-name>]
```

参数：

| 参数          | 是否必须 | 描述                                                  |
| ------------- | -------- | ----------------------------------------------------- |
| `change-name` | 不       | 变更的文件夹名称（如果未提供，则提示输入）            |
| `--schema`    | 不       | 要使用的工作流模式（默认：来自配置或 `spec-driven` ） |

功能：

- 创建 `openspec/changes/<change-name>/` 目录
- 在 change 文件夹中创建 `.openspec.yaml` 元数据文件
- 显示第一个可供创建的工件模板
- 如果未提供名称和架构，则提示更改。

生成物：

```
openspec/changes/<change-name>/
└── .openspec.yaml    # Change metadata (schema, created date)
```

例子：

```
You: /opsx:new add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     Schema: spec-driven

     Ready to create: proposal
     Use /opsx:continue to create it, or /opsx:ff to create all artifacts.
```

Tips: 

- 使用描述性名称： `add-feature` 、 `fix-bug` 、 `refactor-module`
- 避免使用诸如 `update` 、 `changes` 、 `wip` 类的通用名称。
- 也可以在项目配置（ `openspec/config.yaml` ）中设置模式。

------

### `/opsx:continue`

创建依赖链中的下一个构件。每次创建一个构件，以实现增量式开发。

语法：

```
/opsx:continue [change-name]
```

参数：

| 参数          | 是否必须 | 描述                                               |
| ------------- | -------- | -------------------------------------------------- |
| `change-name` | 不       | 要继续执行哪项更改（如果未提供，则根据上下文推断） |

功能：

- 查询构件依赖关系图
- 显示哪些工件已准备就绪，哪些工件已被阻塞。
- 创建第一个就绪工件
- 读取依赖文件以获取上下文
- 显示创建后可用的内容

例子：

```
You: /opsx:continue

AI:  Change: add-dark-mode

     Artifact status:
     ✓ proposal    (done)
     ◆ specs       (ready)
     ◆ design      (ready)
     ○ tasks       (blocked - needs: specs)

     Creating specs...

     [Reads proposal.md, creates specs based on requirements]

     ✓ Created openspec/changes/add-dark-mode/specs/ui/spec.md

     Now available: tasks
     Run /opsx:continue to create the next artifact.
```

Tips: 

- 当您需要在继续操作之前查看每个工件时，请使用此功能。
- 适用于需要控制的复杂变更。适用于需要控制的复杂变更。
- 多个工件可能同时准备就绪。
- 您可以在继续操作之前编辑已创建的对象。

------

### `/opsx:ff`

快速完成工件创建。一次性创建所有规划工件。

语法：

```
/opsx:ff [change-name]
```

参数：

| 参数          | 是否必须 | 描述                                       |
| ------------- | -------- | ------------------------------------------ |
| `change-name` | 不       | 快进的变更（如果未提供，则根据上下文推断） |

功能：

- 按依赖顺序创建所有构件
- 通过待办事项列表跟踪进度
- 当所有 `apply-required` 工件都完成后停止
- 在创建下一个构件之前，读取每个依赖项。

例子：

```
You: /opsx:ff add-dark-mode

AI:  Fast-forwarding add-dark-mode...

     ✓ Creating proposal.md
     ✓ Creating specs/ui/spec.md
     ✓ Creating design.md
     ✓ Creating tasks.md

     All planning artifacts complete!
     Ready for implementation. Run /opsx:apply to begin.
```

Tips: 

- 当你对要构建的东西有清晰的构想时，可以使用此方法。
- 对于简单的更改，速度比 `/opsx:continue` 更快。
- 之后你仍然可以编辑制品
- 适用于小型到中型功能

------

### `/opsx:apply`

根据变更内容执行任务。逐项完成任务清单，编写代码并逐项勾选。

语法：

```
/opsx:apply [change-name]
```

参数：

| 参数          | 是否必需 | 描述                                           |
| ------------- | -------- | ---------------------------------------------- |
| `change-name` | 不       | 要实施哪项更改（如果未提供，则根据上下文推断） |

功能：

- 读取 `tasks.md` 并识别未完成的任务
- 逐一完成任务
- 根据需要编写代码、创建文件、运行测试。
- 用复选框 `[x]` 标记任务已完成

例子：

```
You: /opsx:apply

AI:  Implementing add-dark-mode...

     Reading tasks.md:
     - [ ] 1.1 Create ThemeContext
     - [ ] 1.2 Add CSS custom properties
     - [ ] 1.3 Implement localStorage persistence

     Working on 1.1: Create ThemeContext...
     [Creates src/context/ThemeContext.tsx]

     ✓ 1.1 Complete

     Working on 1.2: Add CSS custom properties...
     [Updates src/styles/globals.css]

     ✓ 1.2 Complete
     ...
```

**Tips: **

- 如果被打断，可以从上次中断的地方继续。
- 通过指定更改名称来用于并行更改。
- 任务完成状态在 `tasks.md` 复选框中进行跟踪。

------

### `/opsx:verify`

验证实现是否与变更文档相符。检查完整性、正确性和一致性。

语法：

```
/opsx:verify [change-name]
```

参数：

| 参数          | 是否必需 | 描述                                           |
| ------------- | -------- | ---------------------------------------------- |
| `change-name` | 不       | 要验证哪个更改（如果未提供，则根据上下文推断） |

功能：

- 检查实施质量的三个维度
- 搜索代码库以查找实现证据
- 报告问题，并将其归类为“严重”、“警告”或“建议”。
- 不会阻止存档，但会暴露问题。

验证维度：

| 维度                          | 验证内容                                       |
| ----------------------------- | ---------------------------------------------- |
| **Completeness <br />完整性** | 所有任务已完成，所有需求已实现，所有场景已覆盖 |
| **Correctness <br />正确性**  | 实现符合规范意图，并处理了各种特殊情况。       |
| **Coherence <br />相干性**    | 设计决策体现在代码中，模式保持一致             |

例子：

```
You: /opsx:verify

AI:  Verifying add-dark-mode...

     COMPLETENESS
     ✓ All 8 tasks in tasks.md are checked
     ✓ All requirements in specs have corresponding code
     ⚠ Scenario "System preference detection" has no test coverage

     CORRECTNESS
     ✓ Implementation matches spec intent
     ✓ Edge cases from scenarios are handled
     ✓ Error states match spec definitions

     COHERENCE
     ✓ Design decisions reflected in code structure
     ✓ Naming conventions consistent with design.md
     ⚠ Design mentions "CSS variables" but implementation uses Tailwind classes

     SUMMARY
     ─────────────────────────────
     Critical issues: 0
     Warnings: 2
     Ready to archive: Yes (with warnings)

     Recommendations:
     1. Add test for system preference detection
     2. Update design.md to reflect Tailwind usage, or refactor to use CSS variables
```

**Tips: **

- 在归档前运行，以便及早发现不匹配项。
- 警告不会阻止归档，但会指出潜在问题。
- 适合在提交之前审查人工智能的工作成果。
- 可以揭示制品与实现之间的偏差

------

### `/opsx:sync`

可选命令。将变更中的增量规范合并到主规范中。归档程序会在需要时提示同步，因此通常无需手动运行此命令。

语法：

```
/opsx:sync [change-name]
```

参数：

| 参数          | 是否必需的 | 描述                                         |
| ------------- | ---------- | -------------------------------------------- |
| `change-name` | 不         | 要同步的更改（如果未提供，则根据上下文推断） |

功能：

- 从更改文件夹读取增量规范
- 解析新增/修改/删除/重命名部分
- 将更改合并到主 `openspec/specs/` 目录中
- 保留增量中未提及的现有内容
- 不存档更改（保持有效）

例子：

```text
You: /opsx:sync

AI:  Syncing add-dark-mode delta specs...

     Reading: openspec/changes/add-dark-mode/specs/ui/spec.md
     Target:  openspec/specs/ui/spec.md

     Changes to apply:
     ✓ ADDED: Theme Selection requirement (2 scenarios)
     ✓ ADDED: Accessibility requirement (1 scenario)

     Merging...
     ✓ openspec/specs/ui/spec.md updated

     Change remains active. Run /opsx:archive when ready to complete.
```

何时需要手动操作

| 场景                                         | 是否同步？         |
| -------------------------------------------- | ------------------ |
| 长期变更，希望在归档前将规范合并到主分支中。 | 是的               |
| 多项并行变更需要更新基础规范。               | 是的               |
| 想单独预览/查看合并结果                      | 是的               |
| 快速切换，直接进入存档                       | 不（归档会处理）。 |

Tips: 

- 同步是智能的，而不是复制粘贴。
- 可以在现有需求中添加场景，而不会重复。
- 同步后更改仍然有效（不会被存档）
- 大多数用户永远不需要直接调用此功能——如有需要，可进行存档提示。

------

### `/opsx:archive`

归档已完成的更改。最终确定更改并将其移动到归档文件夹。

语法：

```
/opsx:archive [change-name]
```

参数：

| 参数          | 必需 | 描述                                         |
| ------------- | ---- | -------------------------------------------- |
| `change-name` | 不   | 要存档的更改（如果未提供，则根据上下文推断） |

功能：

- 检查工件完成状态
- 检查任务完成情况（如果未完成则发出警告）
- 如果尚未同步，则提供同步增量规格的功能。
- 将更改文件夹移动到 `openspec/changes/archive/YYYY-MM-DD-<name>/`
- 保留所有用于审计跟踪的痕迹

例子：

```text
You: /opsx:archive

AI:  Archiving add-dark-mode...

     Artifact status:
     ✓ proposal.md exists
     ✓ specs/ exists
     ✓ design.md exists
     ✓ tasks.md exists (8/8 tasks complete)

     Delta specs: Not yet synced
     → Sync now? (recommended)

You: Yes

AI:  ✓ Synced specs to openspec/specs/ui/spec.md
     ✓ Moved to openspec/changes/archive/2025-01-24-add-dark-mode/

     Change archived successfully.
```

Tips: 

- 归档不会阻塞未完成的任务，但会发出警告。
- Delta 规格可以在归档期间或归档之前进行同步。
- 存档的更改将被保存以供历史记录。
- 首先使用 `/opsx:verify` 来发现问题

------

### `/opsx:bulk-archive`

一次性归档多个已完成的更改。处理更改之间的规范冲突。

语法：

```
/opsx:bulk-archive [change-names...]
```

参数：

| 参数           | 必需 | 描述                                       |
| -------------- | ---- | ------------------------------------------ |
| `change-names` | 不   | 对存档的具体更改（如果未提供，则提示选择） |

功能：

- 列出所有已完成的更改
- 在归档前验证每次更改
- 检测变更中的规范冲突
- 通过检查实际实现的内容来解决冲突。
- 按时间顺序排列的档案

例子：

```
You: /opsx:bulk-archive

AI:  Found 3 completed changes:
     - add-dark-mode (8/8 tasks complete)
     - fix-login-bug (3/3 tasks complete)
     - update-footer (2/2 tasks complete)

     Checking for spec conflicts...
     ⚠ add-dark-mode and update-footer both touch specs/ui/

     Inspecting codebase to resolve...
     Both changes are implemented. Will merge in chronological order:
     1. add-dark-mode (created Jan 20)
     2. update-footer (created Jan 22)

     Archive all 3 changes?

You: Yes

AI:  ✓ Archived add-dark-mode
     ✓ Archived fix-login-bug
     ✓ Archived update-footer
     Specs merged: add-dark-mode → update-footer
```

Tips: 

- 适用于并行工作流程
- 冲突解决是主动的（检查代码库）
- 更改按创建顺序存档。
- 覆盖规范内容前的提示

------

### `/opsx:onboard`

引导您完成整个 OpenSpec 工作流程。使用您实际代码库的交互式教程。

语法：

```
/opsx:onboard
```

功能：

- 通过旁白完整讲解工作流周期
- 扫描您的代码库，寻找真正的改进机会
- 通过实际成果创造真正的改变
- 执行实际工作（小的、安全的改动）
- 存档已完成的变更
- 逐一解释每个步骤的发生过程。

阶段：

1. 欢迎和代码库分析
2. 寻找改进机会
3. 创建变更（ `/opsx:new` ）
4. 撰写提案
5. 制定规范
6. 编写设计
7. 创建任务
8. 执行任务（ `/opsx:apply` ）
9. 验证实现
10. 存档变更
11. 总结和后续步骤

例子：

```
You: /opsx:onboard

AI:  Welcome to OpenSpec!

     I'll walk you through the complete workflow using your actual codebase.
     We'll find something small to improve, create a proper change for it,
     implement it, and archive it.

     Let me scan your codebase for opportunities...

     [Analyzes codebase]

     I found a few things we could work on:
     1. Add input validation to the contact form
     2. Improve error messages in the auth flow
     3. Add loading states to async buttons

     Which interests you? (or suggest something else)
```

Tips: 

- 最适合学习工作流程的新用户
- 使用真实代码，而非示例代码。
- 带来你可以保留或舍弃的真正改变。
- 完成需要15-30分钟

------

## AI Tool 的命令语法

不同的 AI 工具使用略有不同的命令语法。请使用与您的工具相匹配的格式：

| 工具          | 语法示例                                                     |
| ------------- | ------------------------------------------------------------ |
| Claude Code   | `/opsx:new`, `/opsx:apply` `/opsx:new` ， `/opsx:apply`      |
| Cursor        | `/opsx-new`, `/opsx-apply` `/opsx-new` ， `/opsx-apply`      |
| Windsurf      | `/opsx-new`, `/opsx-apply` `/opsx-new` ， `/opsx-apply`      |
| Copilot (IDE) | `/opsx-new`, `/opsx-apply` `/opsx-new` ， `/opsx-apply`      |
| Trae          | `/openspec-new-change`, `/openspec-apply-change` `/openspec-new-change` ， `/openspec-apply-change`
无论语法如何，功能都相同。 |



------

## 旧版命令



这些命令使用较旧的“一次性执行”工作流程。它们仍然有效，但建议使用 OPSX 命令。

| 命令                 | 作用                                                   |
| -------------------- | ------------------------------------------------------ |
| `/openspec:proposal` | 一次性创建所有文档（提案、规格说明、设计稿、任务清单） |
| `/openspec:apply`    | 实施变更                                               |
| `/openspec:archive`  | 存档更改                                               |

何时使用旧版命令：

- 使用旧工作流程的现有项目
- 简单的更改，无需创建增量工件。
- 偏好非此即彼的方法

## 故障排除

### "Change not found"

该命令无法确定要处理哪个更改。

解决方案：

- 明确指定更改名称： `/opsx:apply add-dark-mode`
- 检查更改文件夹是否存在： `openspec list`
- 请确认您位于正确的项目目录中

### "No artifacts ready" 

所有工件要么已完成，要么因缺少依赖项而阻塞。

**解决方案：**

- 运行 `openspec status --change <name>` 查看阻塞原因
- 检查所需工件是否存在
- 首先创建缺失的依赖项工件

### "Schema not found"

指定的模式不存在。

解决方案：

- 列出可用模式： `openspec schemas`
- 检查模式名称的拼写
- 如果是自定义模式，请创建模式： `openspec schema init <name>`

### Commands not recognized 

该人工智能工具无法识别 OpenSpec 命令。

解决方案：

- 确保 OpenSpec 已初始化： `openspec init`
- 技能再生： `openspec update`
- 检查 `.claude/skills/` 目录是否存在（适用于 Claude Code）
- 重启你的 AI 工具以学习新技能

### Artifacts not generating properly

人工智能会生成不完整或不正确的产物。

解决方案：

- 在 `openspec/config.yaml` 中添加项目上下文
- 添加针对特定工件的规则以提供具体指导
- 请在变更描述中提供更多详细信息。
- 使用 `/opsx:continue` 而不是 `/opsx:ff` 可以获得更多控制权。





