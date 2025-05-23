---
description: Docker 文档中使用的组件和格式示例
title: 提示框
toc_max: 3
---

我们支持以下几大类提示框：

- 警示：注意、提示、重要、警告、谨慎

我们还支持摘要栏，用于表示功能所需的订阅、版本或管理员角色。
要添加摘要栏：

将功能名称添加到 `/data/summary.yaml` 文件中。使用以下属性：

| 属性          | 描述                                              | 可能的值                                            |
|--------------|--------------------------------------------------|--------------------------------------------------|
| `subscription` | 记录使用该功能所需的订阅级别                       | All（全部）, Personal（个人）, Pro（专业）, Team（团队）, Business（企业） |
| `availability` | 记录该功能所处的产品开发阶段                       | Experimental（实验性）, Beta（测试版）, Early Access（早期访问）, GA（正式发布）, Retired（已停用） |
| `requires`     | 记录该功能所需的最低版本                           | 无特定值，使用字符串描述版本并链接到相关发行说明 |
| `for`          | 记录该功能是否面向 IT 管理员                      | Administrators（管理员）                            |

然后，在要添加摘要栏的页面上添加 `summary-bar` 简码。注意，功能名称区分大小写。摘要栏中显示的图标会自动渲染。

## 示例

{{< summary-bar feature_name="PKG 安装程序" >}}

> [!NOTE]
>
> 注意 `get_hit_count` 函数的编写方式。这个基本的重试循环让我们可以在 redis 服务不可用时多次尝试请求。这在应用程序启动过程中很有用，同时也使得我们的应用程序在 Redis 服务需要在应用程序生命周期内的任何时候重启时更具弹性。在集群中，这也有助于处理节点之间的瞬时连接断开。

> [!TIP]
>
> 要获得更小的基础镜像，请使用 `alpine`。

> [!IMPORTANT]
>
> 像对待密码一样对待访问令牌并保持其机密性。安全地存储您的令牌（例如，使用凭据管理器）。

> [!WARNING]
>
> 移除卷
>
> 默认情况下，运行 `docker compose down` 时不会移除 compose 文件中的命名卷。如果您想要移除卷，需要添加 `--volumes` 标志。
>
> Docker Desktop 仪表板在删除应用栈时不会移除卷。

> [!CAUTION]
>
> 此处危险。

对于以下两个提示框，请查阅 [Docker 发布生命周期](/release-lifecycle) 以获取更多关于何时使用它们的信息。

## 格式化

```md
{{</* summary-bar feature_name="PKG 安装程序" */>}}
```

```html
> [!NOTE]
>
> 注意 `get_hit_count` 函数的编写方式。这个基本的重试循环让我们可以在 redis 服务不可用时多次尝试请求。这在应用程序启动过程中很有用，同时也使得我们的应用程序在 Redis 服务需要在应用程序生命周期内的任何时候重启时更具弹性。在集群中，这也有助于处理节点之间的瞬时连接断开。

> [!TIP]
>
> 要获得更小的基础镜像，请使用 `alpine`。

> [!IMPORTANT]
>
> 像对待密码一样对待访问令牌并保持其机密性。安全地存储您的令牌（例如，使用凭据管理器）。

> [!WARNING]
>
> 移除卷
>
> 默认情况下，运行 `docker compose down` 时不会移除 compose 文件中的命名卷。如果您想要移除卷，需要添加 `--volumes` 标志。
>
> Docker Desktop 仪表板在删除应用栈时不会移除卷。

> [!CAUTION]
>
> 此处危险。
```
