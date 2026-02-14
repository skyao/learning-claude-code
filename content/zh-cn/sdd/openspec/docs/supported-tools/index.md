---
title: "支持的工具"
linkTitle: "支持的工具"
weight: 40
date: 2026-02-13
description: >
  OpenSpec 支持的工具
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md

（Gemini 机翻）

----

OpenSpec 支持 20 多种 AI 编码助手。当您运行 `openspec init` 时，系统会提示您选择正在使用的工具，OpenSpec 将自动配置相应的集成。

## 工作原理

对于您选择的每个工具，OpenSpec 会安装：

1.  **技能 (Skills)** — 驱动 `/opsx:*` 工作流命令的可复用指令文件
2.  **命令 (Commands)** — 特定于工具的斜杠命令 (slash command) 绑定

## 工具目录参考

| 工具 (Tool) | 技能位置 (Skills Location) | 命令位置 (Commands Location) |
|------|-----------------|-------------------|
| Amazon Q Developer | `.amazonq/skills/` | `.amazonq/prompts/` |
| Antigravity | `.agent/skills/` | `.agent/workflows/` |
| Auggie (Augment CLI) | `.augment/skills/` | `.augment/commands/` |
| Claude Code | `.claude/skills/` | `.claude/commands/opsx/` |
| Cline | `.cline/skills/` | `.clinerules/workflows/` |
| CodeBuddy | `.codebuddy/skills/` | `.codebuddy/commands/opsx/` |
| Codex | `.codex/skills/` | `~/.codex/prompts/`\* |
| Continue | `.continue/skills/` | `.continue/prompts/` |
| CoStrict | `.cospec/skills/` | `.cospec/openspec/commands/` |
| Crush | `.crush/skills/` | `.crush/commands/opsx/` |
| Cursor | `.cursor/skills/` | `.cursor/commands/` |
| Factory Droid | `.factory/skills/` | `.factory/commands/` |
| Gemini CLI | `.gemini/skills/` | `.gemini/commands/opsx/` |
| GitHub Copilot | `.github/skills/` | `.github/prompts/`\*\* |
| iFlow | `.iflow/skills/` | `.iflow/commands/` |
| Kilo Code | `.kilocode/skills/` | `.kilocode/workflows/` |
| OpenCode | `.opencode/skills/` | `.opencode/command/` |
| Qoder | `.qoder/skills/` | `.qoder/commands/opsx/` |
| Qwen Code | `.qwen/skills/` | `.qwen/commands/` |
| RooCode | `.roo/skills/` | `.roo/commands/` |
| Trae | `.trae/skills/` | `.trae/skills/` (通过 `/openspec-*`) |
| Windsurf | `.windsurf/skills/` | `.windsurf/workflows/` |

\* Codex 命令安装在全局主目录 (`~/.codex/prompts/` 或 `$CODEX_HOME/prompts/`)，而不是项目目录中。

\*\* GitHub Copilot 的 `.github/prompts/*.prompt.md` 文件仅在 **IDE 扩展**（VS Code, JetBrains, Visual Studio）中被识别为自定义斜杠命令。GitHub Copilot CLI 目前不支持从该目录加载自定义提示词——详见 [github/copilot-cli#618](https://github.com/github/copilot-cli/issues/618)。如果您使用 Copilot CLI，作为一种变通方法，您可能需要在 `.github/agents/` 中手动设置 [自定义 Agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)。

## 非交互式设置

对于 CI/CD 或脚本化设置，请使用 `--tools` 标志：

```bash
# 配置特定工具
openspec init --tools claude,cursor

# 配置所有支持的工具
openspec init --tools all

# 跳过工具配置
openspec init --tools none
```

**可用的工具 ID:** `amazon-q`, `antigravity`, `auggie`, `claude`, `cline`, `codebuddy`, `codex`, `continue`, `costrict`, `crush`, `cursor`, `factory`, `gemini`, `github-copilot`, `iflow`, `kilocode`, `opencode`, `qoder`, `qwen`, `roocode`, `trae`, `windsurf`

## 安装内容

对于每个工具，OpenSpec 会生成 10 个驱动 OPSX 工作流的技能文件：

| 技能 (Skill) | 用途 |
|-------|---------|
| `openspec-explore` | 探索想法的思考搭档 |
| `openspec-new-change` | 启动一个新的变更 (change) |
| `openspec-continue-change` | 创建下一个工件 (artifact) |
| `openspec-ff-change` | 快进 (Fast-forward) 完成所有规划工件 |
| `openspec-apply-change` | 实现任务 |
| `openspec-verify-change` | 验证实现的完整性 |
| `openspec-sync-specs` | 将增量规范同步到主规范（可选——如需归档提示词） |
| `openspec-archive-change` | 归档已完成的变更 |
| `openspec-bulk-archive-change` | 一次性归档多个变更 |
| `openspec-onboard` | 贯穿完整工作流周期的引导式入门 |

这些技能通过斜杠命令调用，如 `/opsx:new`、`/opsx:apply` 等。完整列表请参阅 [命令 (Commands)](commands.md)。

## 添加新工具

想要添加对其他 AI 编码助手的支持？请查看 [命令适配器模式 (command adapter pattern)](../CONTRIBUTING.md) 或在 GitHub 上提交 Issue。

