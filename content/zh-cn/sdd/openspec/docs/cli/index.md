---
title: "CLI"
linkTitle: "CLI"
weight: 40
date: 2026-02-13
description: >
  OpenSpec CLI
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/cli.md

Gemini 机翻。

----

OpenSpec CLI (`openspec`) 提供了用于项目初始化、验证、状态检查和管理的终端命令。这些命令与 [命令 (Commands)](commands.md) 文档中介绍的 AI 斜杠命令（如 `/opsx:new`）相辅相成。

## 概要

| 类别 | 命令 | 用途 |
|----------|----------|---------|
| **设置 (Setup)** | `init`, `update` | 在项目中初始化和更新 OpenSpec |
| **浏览 (Browsing)** | `list`, `view`, `show` | 浏览变更 (changes) 和规范 (specs) |
| **验证 (Validation)** | `validate` | 检查变更和规范是否存在问题 |
| **生命周期 (Lifecycle)** | `archive` | 归档已完成的变更 |
| **工作流 (Workflow)** | `status`, `instructions`, `templates`, `schemas` | 支持工件驱动 (Artifact-driven) 的工作流 |
| **模式 (Schemas)** | `schema init`, `schema fork`, `schema validate`, `schema which` | 创建和管理自定义工作流 |
| **配置 (Config)** | `config` | 查看和修改设置 |
| **实用工具 (Utility)** | `feedback`, `completion` | 反馈和 Shell 集成 |

---

## 人类 vs Agent 命令

大多数 CLI 命令设计为供 **人类** 在终端中使用。部分命令通过 JSON 输出支持 **Agent/脚本** 使用。

### 仅限人类使用的命令

这些命令是交互式的，专为终端操作设计：

| 命令 | 用途 |
|---------|---------|
| `openspec init` | 初始化项目（交互式提示） |
| `openspec view` | 交互式仪表盘 |
| `openspec config edit` | 在编辑器中打开配置 |
| `openspec feedback` | 通过 GitHub 提交反馈 |
| `openspec completion install` | 安装 Shell 自动补全 |

### Agent 兼容命令

这些命令支持 `--json` 输出，供 AI Agent 和脚本以编程方式使用：

| 命令 | 人类用途 | Agent 用途 |
|---------|-----------|-----------|
| `openspec list` | 浏览变更/规范 | `--json` 获取结构化数据 |
| `openspec show <item>` | 阅读内容 | `--json` 用于解析 |
| `openspec validate` | 检查问题 | `--all --json` 用于批量验证 |
| `openspec status` | 查看工件进度 | `--json` 获取结构化状态 |
| `openspec instructions` | 获取后续步骤 | `--json` 获取 Agent 指令 |
| `openspec templates` | 查找模板路径 | `--json` 用于路径解析 |
| `openspec schemas` | 列出可用模式 | `--json` 用于模式发现 |

---

## 全局选项

这些选项适用于所有命令：

| 选项 | 描述 |
|--------|-------------|
| `--version`, `-V` | 显示版本号 |
| `--no-color` | 禁用彩色输出 |
| `--help`, `-h` | 显示命令帮助信息 |

---

## 设置命令

### `openspec init`

在项目中初始化 OpenSpec。创建文件夹结构并配置 AI 工具集成。

```
openspec init [path] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `path` | 否 | 目标目录（默认为当前目录） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--tools <list>` | 非交互式配置 AI 工具。使用 `all`、`none` 或逗号分隔的列表 |
| `--force` | 强制自动清理旧文件，无需提示 |

**支持的工具:** `amazon-q`, `antigravity`, `auggie`, `claude`, `cline`, `codex`, `codebuddy`, `continue`, `costrict`, `crush`, `cursor`, `factory`, `gemini`, `github-copilot`, `iflow`, `kilocode`, `opencode`, `qoder`, `qwen`, `roocode`, `windsurf`

**示例:**

```bash
# 交互式初始化
openspec init

# 在特定目录中初始化
openspec init ./my-project

# 非交互式：配置 Claude 和 Cursor
openspec init --tools claude,cursor

# 配置所有支持的工具
openspec init --tools all

# 跳过提示并自动清理旧文件
openspec init --force
```

**创建的内容:**

```
openspec/
├── specs/              # 您的规范（事实来源 Source of Truth）
├── changes/            # 提议的变更
└── config.yaml         # 项目配置

.claude/skills/         # Claude Code 技能文件（如果选择了 claude）
.cursor/rules/          # Cursor 规则文件（如果选择了 cursor）
... (其他工具配置)
```

---

### `openspec update`

升级 CLI 后更新 OpenSpec 指令文件。重新生成 AI 工具配置文件。

```
openspec update [path] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `path` | 否 | 目标目录（默认为当前目录） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--force` | 即使文件已是最新也强制更新 |

**示例:**

```bash
# npm 升级后更新指令文件
npm update @fission-ai/openspec
openspec update
```

---

## 浏览命令

### `openspec list`

列出项目中的变更 (changes) 或规范 (specs)。

```
openspec list [options]
```

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--specs` | 列出规范而不是变更 |
| `--changes` | 列出变更（默认） |
| `--sort <order>` | 排序方式：`recent`（最近，默认）或 `name`（名称） |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# 列出所有活跃变更
openspec list

# 列出所有规范
openspec list --specs

# 脚本使用的 JSON 输出
openspec list --json
```

**输出 (文本):**

```
Active changes:
  add-dark-mode     UI theme switching support
  fix-login-bug     Session timeout handling
```

---

### `openspec view`

显示用于浏览规范和变更的交互式仪表盘。

```
openspec view
```

打开一个基于终端的界面，用于导航项目中的规范和变更。

---

### `openspec show`

显示变更或规范的详细信息。

```
openspec show [item-name] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `item-name` | 否 | 变更或规范的名称（如果省略则提示选择） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--type <type>` | 指定类型：`change` 或 `spec`（如果无歧义则自动检测） |
| `--json` | 以 JSON 格式输出 |
| `--no-interactive` | 禁用提示 |

**变更 (Change) 专用选项:**

| 选项 | 描述 |
|--------|-------------|
| `--deltas-only` | 仅显示增量规范 (delta specs)（JSON 模式） |

**规范 (Spec) 专用选项:**

| 选项 | 描述 |
|--------|-------------|
| `--requirements` | 仅显示需求，排除场景 (scenarios)（JSON 模式） |
| `--no-scenarios` | 排除场景内容（JSON 模式） |
| `-r, --requirement <id>` | 通过从 1 开始的索引显示特定需求（JSON 模式） |

**示例:**

```bash
# 交互式选择
openspec show

# 显示特定变更
openspec show add-dark-mode

# 显示特定规范
openspec show auth --type spec

# JSON 输出用于解析
openspec show add-dark-mode --json
```

---

## 验证命令

### `openspec validate`

验证变更和规范是否存在结构性问题。

```
openspec validate [item-name] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `item-name` | 否 | 要验证的特定项目（如果省略则提示选择） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--all` | 验证所有变更和规范 |
| `--changes` | 验证所有变更 |
| `--specs` | 验证所有规范 |
| `--type <type>` | 当名称有歧义时指定类型：`change` 或 `spec` |
| `--strict` | 启用严格验证模式 |
| `--json` | 以 JSON 格式输出 |
| `--concurrency <n>` | 最大并行验证数（默认：6，或通过 `OPENSPEC_CONCURRENCY` 环境变量设置） |
| `--no-interactive` | 禁用提示 |

**示例:**

```bash
# 交互式验证
openspec validate

# 验证特定变更
openspec validate add-dark-mode

# 验证所有变更
openspec validate --changes

# 验证所有内容并输出 JSON（用于 CI/脚本）
openspec validate --all --json

# 增加并行度的严格验证
openspec validate --all --strict --concurrency 12
```

**输出 (文本):**

```
Validating add-dark-mode...
  ✓ proposal.md valid
  ✓ specs/ui/spec.md valid
  ⚠ design.md: missing "Technical Approach" section

1 warning found
```

**输出 (JSON):**

```json
{
  "version": "1.0.0",
  "results": {
    "changes": [
      {
        "name": "add-dark-mode",
        "valid": true,
        "warnings": ["design.md: missing 'Technical Approach' section"]
      }
    ]
  },
  "summary": {
    "total": 1,
    "valid": 1,
    "invalid": 0
  }
}
```

---

## 生命周期命令

### `openspec archive`

归档已完成的变更，并将增量规范 (delta specs) 合并到主规范中。

```
openspec archive [change-name] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `change-name` | 否 | 要归档的变更（如果省略则提示选择） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `-y, --yes` | 跳过确认提示 |
| `--skip-specs` | 跳过规范更新（用于基础设施/工具/仅文档的变更） |
| `--no-validate` | 跳过验证（需要确认） |

**示例:**

```bash
# 交互式归档
openspec archive

# 归档特定变更
openspec archive add-dark-mode

# 无提示归档 (CI/脚本)
openspec archive add-dark-mode --yes

# 归档不影响规范的工具变更
openspec archive update-ci-config --skip-specs
```

**执行操作:**

1. 验证变更（除非使用了 `--no-validate`）
2. 提示确认（除非使用了 `--yes`）
3. 将增量规范合并到 `openspec/specs/`
4. 将变更文件夹移动到 `openspec/changes/archive/YYYY-MM-DD-<name>/`

---

## 工作流命令

这些命令支持工件驱动 (artifact-driven) 的 OPSX 工作流。它们对于检查进度的人类和确定下一步操作的 Agent 都很有用。

### `openspec status`

显示变更的工件完成状态。

```
openspec status [options]
```

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--change <id>` | 变更名称（如果省略则提示选择） |
| `--schema <name>` | 模式覆盖（自动从变更配置中检测） |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# 交互式状态检查
openspec status

# 特定变更的状态
openspec status --change add-dark-mode

# Agent 使用的 JSON 格式
openspec status --change add-dark-mode --json
```

**输出 (文本):**

```
Change: add-dark-mode
Schema: spec-driven

Artifacts:
  ✓ proposal     proposal.md exists
  ✓ specs        specs/ exists
  ◆ design       ready (requires: specs)
  ○ tasks        blocked (requires: design)

Next: Create design using /opsx:continue
```

**输出 (JSON):**

```json
{
  "change": "add-dark-mode",
  "schema": "spec-driven",
  "artifacts": [
    {"id": "proposal", "status": "complete", "path": "proposal.md"},
    {"id": "specs", "status": "complete", "path": "specs/"},
    {"id": "design", "status": "ready", "requires": ["specs"]},
    {"id": "tasks", "status": "blocked", "requires": ["design"]}
  ],
  "next": "design"
}
```

---

### `openspec instructions`

获取用于创建工件或应用任务的丰富指令。被 AI Agent 用于理解下一步需要创建什么。

```
openspec instructions [artifact] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `artifact` | 否 | 工件 ID: `proposal`, `specs`, `design`, `tasks`, 或 `apply` |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--change <id>` | 变更名称（非交互模式下必填） |
| `--schema <name>` | 模式覆盖 |
| `--json` | 以 JSON 格式输出 |

**特殊情况:** 使用 `apply` 作为工件参数来获取任务实现指令。

**示例:**

```bash
# 获取下一个工件的指令
openspec instructions --change add-dark-mode

# 获取特定工件的指令
openspec instructions design --change add-dark-mode

# 获取应用/实现指令
openspec instructions apply --change add-dark-mode

# 供 Agent 消费的 JSON 格式
openspec instructions design --change add-dark-mode --json
```

**输出包含:**

- 工件的模板内容
- 来自配置的项目上下文
- 来自依赖工件的内容
- 来自配置的每个工件的规则

---

### `openspec templates`

显示模式 (schema) 中所有工件的解析后的模板路径。

```
openspec templates [options]
```

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--schema <name>` | 要检查的模式（默认: `spec-driven`） |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# 显示默认模式的模板路径
openspec templates

# 显示自定义模式的模板
openspec templates --schema my-workflow

# 编程使用的 JSON 格式
openspec templates --json
```

**输出 (文本):**

```
Schema: spec-driven

Templates:
  proposal  → ~/.openspec/schemas/spec-driven/templates/proposal.md
  specs     → ~/.openspec/schemas/spec-driven/templates/specs.md
  design    → ~/.openspec/schemas/spec-driven/templates/design.md
  tasks     → ~/.openspec/schemas/spec-driven/templates/tasks.md
```

---

### `openspec schemas`

列出可用的工作流模式及其描述和工件流程。

```
openspec schemas [options]
```

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
openspec schemas
```

**输出:**

```
Available schemas:

  spec-driven (package)
    The default spec-driven development workflow
    Flow: proposal → specs → design → tasks

  my-custom (project)
    Custom workflow for this project
    Flow: research → proposal → tasks
```

---

## 模式命令 (Schema Commands)

用于创建和管理自定义工作流模式的命令。

### `openspec schema init`

创建一个新的项目本地模式。

```
openspec schema init <name> [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `name` | 是 | 模式名称（kebab-case 短横线命名） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--description <text>` | 模式描述 |
| `--artifacts <list>` | 逗号分隔的工件 ID 列表（默认: `proposal,specs,design,tasks`） |
| `--default` | 设置为项目默认模式 |
| `--no-default` | 不提示设置为默认 |
| `--force` | 覆盖现有模式 |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# 交互式模式创建
openspec schema init research-first

# 具有特定工件的非交互式创建
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

**创建的内容:**

```
openspec/schemas/<name>/
├── schema.yaml           # 模式定义
└── templates/
    ├── proposal.md       # 每个工件的模板
    ├── specs.md
    ├── design.md
    └── tasks.md
```

---

### `openspec schema fork`

将现有模式复制到您的项目中进行自定义。

```
openspec schema fork <source> [name] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `source` | 是 | 要复制的源模式 |
| `name` | 否 | 新模式名称（默认: `<source>-custom`） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--force` | 覆盖现有的目标 |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# Fork 内置的 spec-driven 模式
openspec schema fork spec-driven my-workflow
```

---

### `openspec schema validate`

验证模式的结构和模板。

```
openspec schema validate [name] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `name` | 否 | 要验证的模式（如果省略则验证所有） |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--verbose` | 显示详细验证步骤 |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# 验证特定模式
openspec schema validate my-workflow

# 验证所有模式
openspec schema validate
```

---

### `openspec schema which`

显示模式解析自何处（用于调试优先级）。

```
openspec schema which [name] [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `name` | 否 | 模式名称 |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--all` | 列出所有模式及其来源 |
| `--json` | 以 JSON 格式输出 |

**示例:**

```bash
# 检查模式来源
openspec schema which spec-driven
```

**输出:**

```
spec-driven resolves from: package
  Source: /usr/local/lib/node_modules/@fission-ai/openspec/schemas/spec-driven
```

**模式优先级:**

1. 项目 (Project): `openspec/schemas/<name>/`
2. 用户 (User): `~/.local/share/openspec/schemas/<name>/`
3. 包 (Package): 内置模式

---

## 配置命令

### `openspec config`

查看和修改全局 OpenSpec 配置。

```
openspec config <subcommand> [options]
```

**子命令:**

| 子命令 | 描述 |
|------------|-------------|
| `path` | 显示配置文件位置 |
| `list` | 显示当前所有设置 |
| `get <key>` | 获取特定值 |
| `set <key> <value>` | 设置值 |
| `unset <key>` | 移除键 |
| `reset` | 重置为默认值 |
| `edit` | 在 `$EDITOR` 中打开 |

**示例:**

```bash
# 显示配置文件路径
openspec config path

# 列出所有设置
openspec config list

# 获取特定值
openspec config get telemetry.enabled

# 设置值
openspec config set telemetry.enabled false

# 显式设置字符串值
openspec config set user.name "My Name" --string

# 移除自定义设置
openspec config unset user.name

# 重置所有配置
openspec config reset --all --yes

# 在编辑器中编辑配置
openspec config edit
```

---

## 实用工具命令

### `openspec feedback`

提交关于 OpenSpec 的反馈。创建一个 GitHub Issue。

```
openspec feedback <message> [options]
```

**参数:**

| 参数 | 必填 | 描述 |
|----------|----------|-------------|
| `message` | 是 | 反馈信息 |

**选项:**

| 选项 | 描述 |
|--------|-------------|
| `--body <text>` | 详细描述 |

**要求:** 必须安装并认证 GitHub CLI (`gh`)。

**示例:**

```bash
openspec feedback "Add support for custom artifact types" \
  --body "I'd like to define my own artifact types beyond the built-in ones."
```

---

### `openspec completion`

管理 OpenSpec CLI 的 Shell 自动补全。

```
openspec completion <subcommand> [shell]
```

**子命令:**

| 子命令 | 描述 |
|------------|-------------|
| `generate [shell]` | 输出补全脚本到标准输出 |
| `install [shell]` | 为当前 Shell 安装补全 |
| `uninstall [shell]` | 移除已安装的补全 |

**支持的 Shell:** `bash`, `zsh`, `fish`, `powershell`

**示例:**

```bash
# 安装补全（自动检测 Shell）
openspec completion install

# 为特定 Shell 安装
openspec completion install zsh

# 生成脚本用于手动安装
openspec completion generate bash > ~/.bash_completion.d/openspec

# 卸载
openspec completion uninstall
```

---

## 退出代码 (Exit Codes)

| 代码 | 含义 |
|------|---------|
| `0` | 成功 |
| `1` | 错误（验证失败、文件丢失等） |

---

## 环境变量

| 变量 | 描述 |
|----------|-------------|
| `OPENSPEC_CONCURRENCY` | 批量验证的默认并发数（默认: 6） |
| `EDITOR` 或 `VISUAL` | `openspec config edit` 使用的编辑器 |
| `NO_COLOR` | 设置时禁用彩色输出 |



