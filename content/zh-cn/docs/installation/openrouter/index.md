---
title: "openrouter中转"
linkTitle: "openrouter中转"
weight: 30
date: 2026-01-25
description: >
  通过 openrouter 中转使用 Claude Code
---

https://openrouter.ai/

## 生成和设置 API key

进入 API 密钥管理，生成新的 API key。

然后参考 claudecode 的配置文档：

https://openrouter.ai/docs/guides/guides/claude-code-integration

修改 .zshrc ，增加 cloud code 设置：

```bash
vi ~/.zshrc
```

如果固定使用某一种接口AI中转，可以简单增加以下内容：

```bash
# claude code
# openrouter forward
export ANTHROPIC_BASE_URL="https://openrouter.ai/api"
export ANTHROPIC_AUTH_TOKEN="sk-or-v1-066c4952432be42556efc4b1fxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
export ANTHROPIC_MODEL="anthropic/claude-opus-4.6"
export ANTHROPIC_SMALL_FAST_MODEL="anthropic/claude-sonnet-4.5"
export ANTHROPIC_API_KEY=""
```

但考虑到可能需要切换不同的AI中转，最好还是使用 alias 来设置，方便切换：

```bash
# claude code
# openrouter forward
alias set-claudecode-openrouter='export ANTHROPIC_BASE_URL="https://openrouter.ai/api";export ANTHROPIC_AUTH_TOKEN="sk-or-v1-066c4952432be42556efc4b1fxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";export ANTHROPIC_MODEL="anthropic/claude-opus-4.6";export ANTHROPIC_SMALL_FAST_MODEL="anthropic/claude-sonnet-4.5";export ANTHROPIC_API_KEY=""'
# change to use default model of claude code
alias set-claudecode='set-claudecode-openrouter'
set-claudecode
```

载入：

```bash
source ~/.zshrc
```

## 使用 claude code

创建一个临时目录，然后进入该目录后，执行:

```bash
claude .
```

就可以开始使用 claude code 了。

