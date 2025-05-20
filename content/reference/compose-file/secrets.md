---
title: Secrets 顶级元素
description: 探索 secrets 顶级元素可以具有的所有属性。
keywords: compose, compose specification, secrets, compose file reference
aliases: 
 - /compose/compose-file/09-secrets/
weight: 60
---

Secrets 是 [Configs](configs.md) 的一种变体，专注于敏感数据，对此用途有特定的约束。

服务只有在 `services` 顶级元素中的 [`secrets` 属性](services.md#secrets)明确授予访问权限时才能访问 secrets。

顶级 `secrets` 声明定义或引用授予 Compose 应用程序中服务的敏感数据。secret 的来源可以是 `file` 或 `environment`。

- `file`：secret 使用指定路径处文件的内容创建。
- `environment`：secret 使用主机上环境变量的值创建。

## 示例 1

`server-certificate` secret 在应用程序部署时创建为 `<project_name>_server-certificate`，
通过将 `server.cert` 的内容注册为平台 secret。

```yml
secrets:
  server-certificate:
    file: ./server.cert
```

## 示例 2

`token` secret 在应用程序部署时创建为 `<project_name>_token`，
通过将 `OAUTH_TOKEN` 环境变量的内容注册为平台 secret。

```yml
secrets:
  token:
    environment: "OAUTH_TOKEN"
```

## 其他资源

有关更多信息，请参阅[如何在 Compose 中使用 secrets](/manuals/compose/how-tos/use-secrets.md)。
