---
title: "quickstart"
linkTitle: "quickstart"
weight: 10
date: 2026-02-13
description: >
  OpenSpec quickstart
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md

本指南解释了安装并初始化 OpenSpec 后其工作原理

## 工作原理

OpenSpec 可以帮助你和你的 AI 编码助手在编写任何代码之前就确定要构建的内容。工作流程遵循一个简单的模式：

```bash
┌────────────────────┐
│ Start a Change     │  /opsx:new
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│ Create Artifacts   │  /opsx:ff or /opsx:continue
│ (proposal, specs,  │
│  design, tasks)    │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│ Implement Tasks    │  /opsx:apply
│ (AI writes code)   │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│ Archive & Merge    │  /opsx:archive
│ Specs              │
└────────────────────┘
```

## OpenSpec 的生成物

运行 openspec init 后，您的项目结构如下：

```bash
openspec/
├── specs/              # Source of truth (your system's behavior)
│   └── <domain>/
│       └── spec.md
├── changes/            # Proposed updates (one folder per change)
│   └── <change-name>/
│       ├── proposal.md
│       ├── design.md
│       ├── tasks.md
│       └── specs/      # Delta specs (what's changing)
│           └── <domain>/
│               └── spec.md
└── config.yaml         # Project configuration (optional)
```

两个关键目录：

- `specs/` ： 真理之源。这些规范描述了系统当前的行为方式。按域组织（例如， `specs/auth/` ， `specs/payments/` ）。

- `changes/`： 拟议修改。每个修改都会拥有自己的文件夹，其中包含所有相关文件。修改完成后，其规范将合并到主 `specs/` 目录中。

## 理解制品

每个 change 文件夹都包含指导工作的工件：

| Artifact 制品 | Purpose 目的                                 |
| ------------- | -------------------------------------------- |
| `proposal.md` | "why" and "what" —— 概括了意图、范围和方法。 |
| `specs/`      | Delta 规格表显示了新增/修改/移除的需求       |
| `design.md`   | "how" —— 技术方法和架构决策                  |
| `tasks.md`    | 实施清单（含复选框）                         |

各种制品是相互依存的：

```bash
proposal ──► specs ──► design ──► tasks ──► implement
   ▲           ▲          ▲                    │
   └───────────┴──────────┴────────────────────┘
            update as you learn
```

在实施过程中，随着你不断学习，你可以随时回头改进之前的制品。

## 增量规范如何运作

增量规范（Delta Spec）是 OpenSpec 中的关键概念。它们显示了相对于当前规范发生了哪些变化。

### 格式

增量规范使用章节（Sections）来指示变更类型：

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
The system MUST require a second factor during login.

#### Scenario: OTP required
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented

## MODIFIED Requirements

### Requirement: Session Timeout
The system SHALL expire sessions after 30 minutes of inactivity.
(Previously: 60 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements

### Requirement: Remember Me
(Deprecated in favor of 2FA)
```

## 归档的内容

当归档更改时：

1. **新增** 需求将附加到主规范中。
2. **修改后的** 需求将取代现有版本
3. **已移除的** 需求将从主规范中删除。

change 文件夹移动到 `openspec/changes/archive/` 以便进行审核历史记录。

## 例如：你的第一次变更

让我们一起来看看如何为应用程序添加深色模式。

1. 开始变更

    ```markdown
    You: /opsx:new add-dark-mode

    AI:  Created openspec/changes/add-dark-mode/
         Ready to create: proposal
    ```

2. 创建工件

	使用 `/opsx:ff` （快进 / fast-forward）一次性创建所有规划工件：

	```markdown

	You: /opsx:ff

	AI:  Creating artifacts for add-dark-mode...
     	✓ proposal.md — why we're doing this, what's changing
     	✓ specs/       — requirements and scenarios
     	✓ design.md    — technical approach
     	✓ tasks.md     — implementation checklist
     	Ready for implementation!

	```

3. 生成了什么

	proposal.md - 阐明意图：

	```markdown
    # Proposal: Add Dark Mode
   
    ## Intent
    Users have requested a dark mode option to reduce eye strain
    during nighttime usage.
   
    ## Scope
    - Add theme toggle in settings
    - Support system preference detection
    - Persist preference in localStorage
   
    ## Approach
    Use CSS custom properties for theming with a React context
    for state management.
   ```

	specs/ui/spec.md - Delta 显示新需求：

    ```markdown
    # Delta for UI
   
    ## ADDED Requirements
   
    ### Requirement: Theme Selection
    The system SHALL allow users to choose between light and dark themes.
   
    #### Scenario: Manual toggle
    - GIVEN a user on any page
    - WHEN the user clicks the theme toggle
    - THEN the theme switches immediately
    - AND the preference persists across sessions
   
    #### Scenario: System preference
    - GIVEN a user with no saved preference
    - WHEN the application loads
    - THEN the system's preferred color scheme is used
    ```

    tasks.md - 实施清单：
   
    ```markdown
    # Tasks
   
    ## 1. Theme Infrastructure
    - [ ] 1.1 Create ThemeContext with light/dark state
    - [ ] 1.2 Add CSS custom properties for colors
    - [ ] 1.3 Implement localStorage persistence
   
    ## 2. UI Components
    - [ ] 2.1 Create ThemeToggle component
    - [ ] 2.2 Add toggle to settings page
    - [ ] 2.3 Update Header to include quick toggle
   
    ## 3. Styling
    - [ ] 3.1 Define dark theme color palette
    - [ ] 3.2 Update components to use CSS variables
    ```

4. 实施

    ```markdown
    You: /opsx:apply
    
    AI:  Working through tasks...
         ✓ 1.1 Created ThemeContext with light/dark state
         ✓ 1.2 Added CSS custom properties to globals.css
         ✓ 1.3 Implemented localStorage persistence
         ✓ 2.1 Created ThemeToggle component
         ...
         All tasks complete!
    ```

	在实施过程中，如果发现设计需要调整，只需更新制品并继续即可。

5. 归档

    ```markdown
    
    You: /opsx:archive
    
    AI:  Archiving add-dark-mode...
         ✓ Merged specs into openspec/specs/ui/spec.md
         ✓ Moved to openspec/changes/archive/2025-01-24-add-dark-mode/
         Done! Ready for the next feature.
    ```
    
    增量规格现在是主规格的一部分，用于记录系统是如何工作的。
    
## 核实与审核

使用命令行界面 (CLI) 检查更改：

```bash
# List active changes
openspec list

# View change details
openspec show add-dark-mode

# Validate spec formatting
openspec validate add-dark-mode

# Interactive dashboard
openspec view
```