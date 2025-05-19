---
description: Docker 文档中使用的组件和格式示例
title: 卡片
toc_max: 3
grid:
- title: Docker Desktop
  description: 桌面版 Docker。
  icon: install_desktop
  link: /desktop/
- title: Docker Engine
  description: 强大的引擎
  icon: developer_board
  link: /engine/
- title: Docker Build
  description: 构建工具
  icon: build
  link: /build/
- title: Docker Compose
  description: 编排工具
  icon: account_tree
  link: /compose/
- title: Docker Hub
  description: 丰富的内容库
  icon: hub
  link: /docker-hub/
---

可以使用 `card` 简码将卡片添加到页面中。
此简码的参数包括：

| 参数        | 描述                                                     |
| ----------- | -------------------------------------------------------- |
| title       | 卡片标题                                                 |
| icon        | 卡片图标标识                                             |
| image       | 使用自定义图片替代图标（与 icon 互斥）                  |
| link        | （可选）点击卡片时的链接目标                            |
| description | Markdown 格式的描述文本                                  |

> [!NOTE]
>
> 卡片的 Markdown 描述存在一个已知限制，
> 即它们不能包含相对链接（指向其他 .md 文档的链接）。
> 此类链接无法正确渲染。相反，请使用您要链接到的页面的
> 绝对 URL 路径。
>
> 例如，不要链接到 `../install/linux.md`，而是写成：
> `/engine/install/linux/`。

## 示例

{{< card
  title="开始使用 Docker"
  icon=favorite
  link=https://docs.docker.com/
  description="使用 Docker 构建、共享和运行您的应用程序" >}}

## 标记语法

```go
{{</* card
  title="开始使用 Docker"
  icon=favorite
  link=https://docs.docker.com/
  description="使用 Docker 构建、共享和运行您的应用程序"
*/>}}
```

### 网格

还有一个内置的 `grid` 简码，用于生成卡片网格。
在大屏幕上网格大小为 3x3，在中等屏幕上为 2x2，在小屏幕上为单列。

{{< grid >}}

网格是一个独立于 `card` 的简码，但在底层实现了相同的组件。
插入网格的标记语法略有不同。网格简码不需要任何参数。
它只是让您指定想要在页面上何处显示网格。

```go
{{</* grid */>}}
```

网格的数据在页面的前置信息中的 `grid` 键下定义，如下所示：

```yaml
# 页面的前置信息部分
title: 某个页面
grid:
  - title: "Docker Engine"
    description: 强大的引擎
    icon: "developer_board"
    link: "/engine/"
  - title: "Docker Build"
    description: 构建工具
    icon: "build"
    link: "/build/"
```