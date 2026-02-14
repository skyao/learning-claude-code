---
title: "多语言"
linkTitle: "多语言"
weight: 70
date: 2026-02-13
description: >
  OpenSpec 多语言
---

https://github.com/Fission-AI/OpenSpec/blob/main/docs/multi-language.md

（Gemini 机翻）

----

配置 OpenSpec 以生成除英语以外其他语言的工件 (artifacts)。

## 快速设置

在您的 `openspec/config.yaml` 文件中添加语言指令：

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
  (语言：葡萄牙语 (pt-BR)
  所有工件必须用巴西葡萄牙语撰写。)

  # 下面是您的其他项目上下文...
  Tech stack: TypeScript, React, Node.js
```

就是这样。所有生成的工件现在都将是葡萄牙语。

## 语言示例

### 葡萄牙语 (巴西)

```yaml
context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
```

### 西班牙语

```yaml
context: |
  Idioma: Español
  Todos los artefactos deben escribirse en español.
```

### 中文 (简体)

```yaml
context: |
  语言：中文（简体）
  所有产出物必须用简体中文撰写。
```

### 日语

```yaml
context: |
  言語：日本語
  すべての成果物は日本語で作成してください。
```

### 法语

```yaml
context: |
  Langue : Français
  Tous les artefacts doivent être rédigés en français.
```

### 德语

```yaml
context: |
  Sprache: Deutsch
  Alle Artefakte müssen auf Deutsch verfasst werden.
```

## 技巧

### 处理专业术语

决定如何处理技术术语：

```yaml
context: |
  Language: Japanese
  Write in Japanese, but:
  - Keep technical terms like "API", "REST", "GraphQL" in English
  - Code examples and file paths remain in English
  (语言：日语
  请用日语撰写，但是：
  - 保留 "API"、"REST"、"GraphQL" 等技术术语为英文
  - 代码示例和文件路径保留为英文)
```

### 与其他上下文结合

语言设置可以与您的其他项目上下文一起工作：

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.

  Tech stack: TypeScript, React 18, Node.js 20
  Database: PostgreSQL with Prisma ORM
```

## 验证

要验证您的语言配置是否生效：

```bash
# 检查指令 - 应该显示您的语言上下文
openspec instructions proposal --change my-change

# 输出将包含您的语言上下文信息
```

