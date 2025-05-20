---
title: 版本和名称顶级元素
description: 了解何时以及是否设置版本和名称顶级元素
keywords: compose, compose specification, services, compose file reference
aliases:
 - /compose/compose-file/04-version-and-name/
weight: 10
---

## 版本顶级元素（已废弃）

顶级 `version` 属性由 Compose 规范定义，用于向后兼容。它仅作为信息提供，如果使用它，您将收到一条警告消息，说明它已废弃。

Compose 不使用 `version` 来选择确切的模式来验证 Compose 文件，而是在实现时优先使用最新的模式。

Compose 会验证是否能够完全解析 Compose 文件。如果某些字段未知，通常是因为 Compose 文件使用了由较新版本的规范定义的字段，您将收到一条警告消息。

## 名称顶级元素

顶级 `name` 属性由 Compose 规范定义，如果您没有明确设置项目名称，则使用此名称。
Compose 提供了一种方式来覆盖此名称，如果未设置顶级 `name` 元素，则设置一个默认的项目名称。

每当通过顶级 `name` 或某些自定义机制定义项目名称时，它都会作为 `COMPOSE_PROJECT_NAME` 暴露给[插值](interpolation.md)和环境变量解析

```yml
name: myapp

services:
  foo:
    image: busybox
    command: echo "I'm running ${COMPOSE_PROJECT_NAME}"
```

有关命名 Compose 项目的其他方法的更多信息，请参阅[指定项目名称](/manuals/compose/how-tos/project-name.md)。
