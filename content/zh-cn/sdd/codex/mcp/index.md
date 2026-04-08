---
title: "MCP"
linkTitle: "MCP"
weight: 30
date: 2026-02-13
description: >
  CodeX MCP
---

https://developers.openai.com/codex/mcp/

----

模型上下文协议 (MCP) 旨在连接模型与工具及上下文。利用它，您可以让 Codex 访问第三方文档，或使其能够与浏览器、Figma 等开发者工具进行交互。

Codex 在命令行界面 (CLI) 和 IDE 扩展中均支持 MCP 服务器。

## 支持的 MCP 功能

- **STDIO 服务器**：作为本地进程运行的服务器（通过命令启动）。
  - 支持环境变量
- **流式 HTTP 服务器 (Streamable HTTP servers)**：通过地址访问的服务器。
  - 支持 Bearer 令牌认证
  - 支持 OAuth 认证（对于支持 OAuth 的服务器，请运行 `codex mcp login <server-name>`）

## 将 Codex 连接到 MCP 服务器

Codex 将 MCP 配置存储在 `config.toml` 中，与其他 Codex 配置设置并列。默认路径为 `~/.codex/config.toml`，但您也可以在项目中使用 `.codex/config.toml` 来配置项目级的 MCP 服务器（仅限受信任的项目）。

CLI 和 IDE 扩展共享此配置。一旦配置好 MCP 服务器，您可以在两个 Codex 客户端之间切换而无需重新设置。

要配置 MCP 服务器，请选择以下一种方式：

1.  **使用 CLI**：运行 `codex mcp` 来添加和管理服务器。
2.  **编辑 `config.toml`**：直接更新 `~/.codex/config.toml`（或受信任项目中的项目级 `.codex/config.toml`）。

### 使用 CLI 进行配置

#### 添加 MCP 服务器

```bash
codex mcp add <server-name> --env VAR1=VALUE1 --env VAR2=VALUE2 -- <stdio server-command>
```

例如，要添加 Context7（一个免费的开发者文档 MCP 服务器），您可以运行以下命令：

```bash
codex mcp add context7 -- npx -y @upstash/context7-mcp
```

#### 其他 CLI 命令

要查看所有可用的 MCP 命令，可以运行 `codex mcp --help`。

#### 终端用户界面 (TUI)

在 `codex` TUI 中，使用 `/mcp` 查看当前活动的 MCP 服务器。

### 使用 config.toml 进行配置

如果需要对 MCP 服务器选项进行更细粒度的控制，请编辑 `~/.codex/config.toml`（或项目级的 `.codex/config.toml`）。在 IDE 扩展中，从齿轮图标菜单选择 **MCP settings** > **Open config.toml**。

在配置文件中，使用 `[mcp_servers.<server-name>]` 表来配置每个 MCP 服务器。

#### STDIO 服务器

- `command`（必填）：启动服务器的命令。
- `args`（可选）：传递给服务器的参数。
- `env`（可选）：为服务器设置的环境变量。
- `env_vars`（可选）：允许并转发的环境变量列表。
- `cwd`（可选）：启动服务器的工作目录。

#### 流式 HTTP 服务器

- `url`（必填）：服务器地址。
- `bearer_token_env_var`（可选）：用于在 `Authorization` 头中发送 Bearer 令牌的环境变量名称。
- `http_headers`（可选）：包含静态值的请求头映射表 (Map)。
- `env_http_headers`（可选）：请求头名称到环境变量名称的映射表（值从环境中获取）。

#### 其他配置选项

- `startup_timeout_sec`（可选）：服务器启动超时时间（秒）。默认值：`10`。
- `tool_timeout_sec`（可选）：服务器运行工具的超时时间（秒）。默认值：`60`。
- `enabled`（可选）：设置为 `false` 可在不删除服务器的情况下禁用它。
- `required`（可选）：设置为 `true` 表示如果该已启用的服务器无法初始化，则启动失败。
- `enabled_tools`（可选）：工具白名单。
- `disabled_tools`（可选）：工具黑名单（在 `enabled_tools` 之后应用）。

如果您的 OAuth 提供商需要静态回调 URI，请在 `config.toml` 中设置顶级配置项 `mcp_oauth_callback_port`。如果未设置，Codex 将绑定到一个临时端口。

#### config.toml 示例

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]

[mcp_servers.context7.env]
MY_ENV_VAR = "MY_ENV_VALUE"
```

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_OAUTH_TOKEN"
http_headers = { "X-Figma-Region" = "us-east-1" }
```

```toml
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
enabled_tools = ["open", "screenshot"]
disabled_tools = ["screenshot"] # 在 enabled_tools 之后应用
startup_timeout_sec = 20
tool_timeout_sec = 45
enabled = true
```

## 常用 MCP 服务器示例

MCP 服务器列表仍在不断增长。以下是一些常见的服务器：

- [OpenAI Docs MCP](https://developers.openai.com/resources/docs-mcp)：搜索和阅读 OpenAI 开发者文档。
- [Context7](https://github.com/upstash/context7)：连接到最新的开发者文档。
- Figma [Local](https://developers.figma.com/docs/figma-mcp-server/local-server-installation/) 和 [Remote](https://developers.figma.com/docs/figma-mcp-server/remote-server-installation/)：访问您的 Figma 设计。
- [Playwright](https://www.npmjs.com/package/@playwright/mcp)：使用 Playwright 控制和检查浏览器。
- [Chrome Developer Tools](https://github.com/ChromeDevTools/chrome-devtools-mcp/)：控制和检查 Chrome 浏览器。
- [Sentry](https://docs.sentry.io/product/sentry-mcp/#codex)：访问 Sentry 日志。
- [GitHub](https://github.com/github/github-mcp-server)：管理 GitHub（功能超出 `git` 支持范围，例如拉取请求和议题）。
