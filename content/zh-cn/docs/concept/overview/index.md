---
title: "概述"
linkTitle: "概述"
weight: 10
date: 2026-01-25
description: >
  Claude Code 的概念概述
---



| 特性                                                         | 位置                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Subagents](https://code.claude.com/docs/en/sub-agents)      | `.claude/agents/<name>.md`                                   | 处于全新独立环境中的自主行动者——自定义工具、权限、模型、内存和持久身份 |
| [Commands](https://code.claude.com/docs/en/slash-commands)   | `.claude/commands/<name>.md`                                 | 将知识注入现有上下文——用于工作流编排的简单用户调用提示模板   |
| [**Skills**](https://code.claude.com/docs/en/skills)         | `.claude/skills/<name>/SKILL.md`                             | 将知识注入现有上下文——可配置、可预加载、可自动发现，支持上下文分支和渐进式披露 |
| [Workflows](https://code.claude.com/docs/en/common-workflows) | [`.claude/commands/weather-orchestrator.md`](https://github.com/shanraisshan/claude-code-best-practice/blob/main/.claude/commands/weather-orchestrator.md) |                                                              |
| [Hooks](https://code.claude.com/docs/en/hooks)               |                                                              | 用户自定义处理程序（脚本、HTTP、提示、代理）在特定事件发生时于代理循环之外运行 |
| [MCP Servers](https://code.claude.com/docs/en/mcp)           | `.claude/settings.json`, `.mcp.json`                         | 模型上下文协议与外部工具、数据库和 API 的连接                |
| [Plugins](https://code.claude.com/docs/en/plugins)           | 可分发软件包                                                 | skill包、subagent、hook、MCP 服务器和 LSP 服务器             |
| [Settings](https://code.claude.com/docs/en/settings)         | `.claude/settings.json`                                      | 分层配置系统                                                 |
| [Status Line](https://code.claude.com/docs/en/statusline)    | `.claude/settings.json`                                      | 可自定义状态栏，显示上下文使用情况、型号、成本和会话信息     |
| [Memory](https://code.claude.com/docs/en/memory)             | `CLAUDE.md`, `.claude/rules/`, `~/.claude/rules/`, `~/.claude/projects/<project>/memory/` | 通过 CLAUDE.md 文件和 `@path` 导入实现持久上下文             |
| [Checkpointing](https://code.claude.com/docs/en/checkpointing) | 自动（基于 git）                                             | 使用回退（ `Esc Esc` 或 `/rewind` ）自动跟踪文件编辑并进行定向摘要 |
| [CLI Startup Flags](https://code.claude.com/docs/en/cli-reference) | `claude [flags]`                                             | 启动 Claude Code 的命令行标志、子命令和环境变量              |
| AI Terms <br />人工智能术语                                  |                                                              | 智能体工程 · 上下文工程 · 氛围编码                           |
| [Best Practices](https://code.claude.com/docs/en/best-practices) |                                                              | 官方最佳实践                                                 |
