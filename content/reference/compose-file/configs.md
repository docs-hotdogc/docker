---
title: 配置顶级元素
description: 探索配置顶级元素可以具有的所有属性。
keywords: compose, compose specification, configs, compose file reference
aliases: 
 - /compose/compose-file/08-configs/
weight: 50
---

{{% include "compose/configs.md" %}}

服务只有在 `services` 顶级元素中的 [`configs`](services.md#configs) 属性明确授予时才能访问配置。

默认情况下，配置：
- 由运行容器命令的用户拥有，但可以通过服务配置覆盖。
- 具有全局可读权限（模式 0444），除非服务配置为覆盖此权限。

顶级 `configs` 声明定义或引用授予给 Compose 应用程序中服务的配置数据。配置的来源是 `file` 或 `external`。

- `file`：使用指定路径的文件内容创建配置。
- `environment`：使用环境变量的值创建配置内容。在 Docker Compose 版本 [2.23.1](/manuals/compose/releases/release-notes.md#2231) 中引入。
- `content`：使用内联值创建内容。在 Docker Compose 版本 [2.23.1](/manuals/compose/releases/release-notes.md#2231) 中引入。
- `external`：如果设置为 true，`external` 指定此配置已经创建。Compose 不会尝试创建它，如果它不存在，则会发生错误。
- `name`：在容器引擎中查找的配置对象的名称。此字段可用于引用包含特殊字符的配置。名称按原样使用，并且**不会**与项目名称一起限定范围。

## 示例 1

`<project_name>_http_config` 在部署应用程序时创建，通过将 `httpd.conf` 的内容注册为配置数据。

```yml
configs:
  http_config:
    file: ./httpd.conf
```

或者，`http_config` 可以声明为外部配置。Compose 查找 `http_config` 以向相关服务公开配置数据。

```yml
configs:
  http_config:
    external: true
```

## 示例 2

`<project_name>_app_config` 在部署应用程序时创建，通过将内联内容注册为配置数据。这意味着 Compose 在创建配置时会推断变量，允许您根据服务配置调整内容：

```yml
configs:
  app_config:
    content: |
      debug=${DEBUG}
      spring.application.admin.enabled=${DEBUG}
      spring.application.name=${COMPOSE_PROJECT_NAME}
```

## 示例 3

外部配置查找也可以通过指定 `name` 使用不同的键。

以下示例修改了前一个示例，使用参数 `HTTP_CONFIG_KEY` 查找配置。实际查找键在部署时通过变量的[插值](interpolation.md)设置，但作为硬编码 ID `http_config` 暴露给容器。

```yml
configs:
  http_config:
    external: true
    name: "${HTTP_CONFIG_KEY}"
```

如果 `external` 设置为 `true`，除了 `name` 之外的所有其他属性都无关紧要。如果 Compose 检测到任何其他属性，它会将 Compose 文件视为无效。
