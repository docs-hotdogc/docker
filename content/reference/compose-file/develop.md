---
title: Compose 开发规范
description: 了解 Compose 开发规范
keywords: compose, compose specification, compose file reference, compose develop specification
aliases:
 - /compose/compose-file/develop/
weight: 150
---

> [!NOTE] 
>
> 开发是 Compose 规范的可选部分。它在 Docker Compose 版本 2.22.0 及更高版本中可用。

{{% include "compose/develop.md" %}}

本页定义了 Compose 如何高效地协助您，并定义了 Compose 设置的开发约束和工作流程。只有一部分 Compose 文件服务可能需要 `develop` 子部分。

## 示例

```yaml
services:
  frontend:
    image: example/webapp
    build: ./webapp
    develop:
      watch: 
        # 同步静态内容
        - path: ./webapp/html
          action: sync
          target: /var/www
          ignore:
            - node_modules/

  backend:
    image: example/backend
    build: ./backend
    develop:
      watch: 
        # 重建镜像并重新创建服务
        - path: ./backend/src
          action: rebuild
```

## 属性

<!-- vale Docker.HeadingSentenceCase = NO ) -->

`develop` 子部分定义了 Compose 应用的配置选项，以通过优化的工作流程在服务开发期间协助您。

### `watch`

`watch` 属性定义了一个规则列表，用于控制基于本地文件更改的自动服务更新。`watch` 是一个序列，序列中的每个项目都定义了 Compose 应用于监控源代码更改的规则。有关更多信息，请参阅[使用 Compose Watch](/manuals/compose/how-tos/file-watch.md)。

#### `action`

`action` 定义检测到更改时要采取的操作。如果 `action` 设置为：

- `rebuild`：Compose 基于 `build` 部分重建服务镜像，并使用更新的镜像重新创建服务。
- `restart`：Compose 重启服务容器。在 Docker Compose 版本 2.32.0 及更高版本中可用。
- `sync`：Compose 保持现有服务容器运行，但根据 `target` 属性将源文件与容器内容同步。
- `sync+restart`：Compose 根据 `target` 属性将源文件与容器内容同步，然后重启容器。在 Docker Compose 版本 2.23.0 及更高版本中可用。
- `sync+exec`：Compose 根据 `target` 属性将源文件与容器内容同步，然后在容器内执行命令。在 Docker Compose 版本 2.32.0 及更高版本中可用。

#### `exec`

{{< summary-bar feature_name="Compose exec" >}}

`exec` 仅在 `action` 设置为 `sync+exec` 时相关。与[服务钩子](services.md#post_start)类似，`exec` 用于定义容器启动后要在容器内运行的命令。

- `command`：指定容器启动后要运行的命令。此属性是必需的，您可以选择使用 shell 形式或 exec 形式。
- `user`：运行命令的用户。如果未设置，命令将以与主服务命令相同的用户运行。
- `privileged`：允许命令以特权访问运行。
- `working_dir`：运行命令的工作目录。如果未设置，它将在与主服务命令相同的工作目录中运行。
- `environment`：设置运行命令的环境变量。虽然命令继承了为服务主命令定义的环境变量，但此部分允许您添加新变量或覆盖现有变量。

```yaml
services:
  frontend:
    image: ...
    develop:
      watch: 
        # 同步内容然后运行命令以不间断地重新加载服务
        - path: ./etc/config
          action: sync+exec
          target: /etc/config/
          exec:
            command: app reload
```

#### `ignore`

`ignore` 属性可用于定义要忽略的路径模式列表。任何匹配模式或属于匹配模式的文件夹的更新文件都不会触发服务重新创建。
语法与 `.dockerignore` 文件相同：

- `*` 匹配文件名中的 0 个或多个字符。
- `?` 匹配文件名中的单个字符。
- `*/*` 匹配具有任意名称的两个嵌套文件夹
- `**` 匹配任意数量的嵌套文件夹

如果构建上下文包含 `.dockerignore` 文件，则此文件中的模式将作为 `ignores` 文件的隐式内容加载，并附加在 Compose 模型中设置的值。

#### `include`

有时，选择要监视的文件比使用 `ignore` 声明不应监视的文件更容易。

`include` 属性可用于定义要监视的路径的模式或模式列表。
只有匹配这些模式的文件才会在应用监视规则时被考虑。语法与 `ignore` 相同。

```yaml
services:
  backend:
    image: example/backend
    develop:
      watch: 
        # 重建镜像并重新创建服务
        - path: ./src
          include: *.go  
          action: rebuild
```

#### `path`

`path` 属性定义要监视更改的源代码路径（相对于项目目录）。对路径内任何不匹配任何 `ignore` 规则的文件进行更新都会触发配置的操作。

#### `target`

`target` 属性仅在 `action` 配置为 `sync` 时适用。`path` 内有更改的文件与容器的文件系统同步，以便后者始终使用最新内容运行。

```yaml
services:
  frontend:
    image: example/webapp
    develop:
      watch: 
        # 同步静态内容
        - path: ./webapp/html
          action: sync
          target: /var/www
          ignore:
            - node_modules/
```

### `watch`

`watch` 属性定义了一个规则列表，用于控制基于本地文件更改的自动服务更新。`watch` 是一个序列，序列中的每个项目都定义了 Compose 应用于监控源代码更改的规则。有关更多信息，请参阅[使用 Compose Watch](/manuals/compose/how-tos/file-watch.md)。

#### `action`

`action` 定义检测到更改时要采取的操作。如果 `action` 设置为：

- `rebuild`：Compose 基于 `build` 部分重建服务镜像，并使用更新的镜像重新创建服务。
- `restart`：Compose 重启服务容器。在 Docker Compose 版本 2.32.0 及更高版本中可用。
- `sync`：Compose 保持现有服务容器运行，但根据 `target` 属性将源文件与容器内容同步。
- `sync+restart`：Compose 根据 `target` 属性将源文件与容器内容同步，然后重启容器。在 Docker Compose 版本 2.23.0 及更高版本中可用。
- `sync+exec`：Compose 根据 `target` 属性将源文件与容器内容同步，然后在容器内执行命令。在 Docker Compose 版本 2.32.0 及更高版本中可用。

#### `exec`

{{< summary-bar feature_name="Compose exec" >}}

`exec` 仅在 `action` 设置为 `sync+exec` 时相关。与[服务钩子](services.md#post_start)类似，`exec` 用于定义容器启动后要在容器内运行的命令。

- `command`：指定容器启动后要运行的命令。此属性是必需的，您可以选择使用 shell 形式或 exec 形式。
- `user`：运行命令的用户。如果未设置，命令将以与主服务命令相同的用户运行。
- `privileged`：允许命令以特权访问运行。
- `working_dir`：运行命令的工作目录。如果未设置，它将在与主服务命令相同的工作目录中运行。
- `environment`：设置运行命令的环境变量。虽然命令继承了为服务主命令定义的环境变量，但此部分允许您添加新变量或覆盖现有变量。

```yaml
services:
  frontend:
    image: ...
    develop:
      watch: 
        # 同步内容然后运行命令以不间断地重新加载服务
        - path: ./etc/config
          action: sync+exec
          target: /etc/config/
          exec:
            command: app reload
```

#### `ignore`

`ignore` 属性可用于定义要忽略的路径模式列表。任何匹配模式或属于匹配模式的文件夹的更新文件都不会触发服务重新创建。
语法与 `.dockerignore` 文件相同：

- `*` 匹配文件名中的 0 个或多个字符。
- `?` 匹配文件名中的单个字符。
- `*/*` 匹配具有任意名称的两个嵌套文件夹
- `**` 匹配任意数量的嵌套文件夹

如果构建上下文包含 `.dockerignore` 文件，则此文件中的模式将作为 `ignores` 文件的隐式内容加载，并附加在 Compose 模型中设置的值。

#### `include`

有时，选择要监视的文件比使用 `ignore` 声明不应监视的文件更容易。

`include` 属性可用于定义要监视的路径的模式或模式列表。
只有匹配这些模式的文件才会在应用监视规则时被考虑。语法与 `ignore` 相同。

```yaml
services:
  backend:
    image: example/backend
    develop:
      watch: 
        # 重建镜像并重新创建服务
        - path: ./src
          include: *.go  
          action: rebuild
```

#### `path`

`path` 属性定义要监视更改的源代码路径（相对于项目目录）。对路径内任何不匹配任何 `ignore` 规则的文件进行更新都会触发配置的操作。

#### `target`

`target` 属性仅在 `action` 配置为 `sync` 时适用。`path` 内有更改的文件与容器的文件系统同步，以便后者始终使用最新内容运行。
