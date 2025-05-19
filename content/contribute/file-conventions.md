---
title: 源文件约定
description: 新建的 .md 文件应如何格式化
keywords: 源文件, 贡献, 风格指南
weight: 30
---

## 文件名

当您创建新的 .md 文件用于新内容时，请确保： 
- 文件名尽可能简短
- 尽量将文件名控制在一到两个单词
- 使用短横线分隔单词。例如：
  - `add-seats.md` 和 `remove-seats.md`
  - `multiplatform-images` 优于 `multi-platform-images`

## 前置信息

页面的前置信息位于 Markdown 文件顶部的一个部分，以三个短横线开始和结束。它包含 YAML 格式的内容。支持以下键值。标题、描述和关键词是必需的。

| 键              | 必需   | 描述                                                                                                                                                                                                    |
|-----------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| title           | 是      | 页面标题。这将作为 `<h1>` 级别的标题添加到 HTML 输出中。                                                                                                                                     |
| description     | 是      | 描述页面内容的一句话。这将添加到 HTML 元数据中。它不会在页面上显示。                                                                                                |
| keywords        | 是      | 以逗号分隔的关键词列表。这些将添加到 HTML 元数据中。                                                                                                                                      |
| aliases         | 否      | 应重定向到当前页面的页面 YAML 列表。在构建时，此处列出的每个页面都会创建为包含 302 重定向到此页面的 HTML 存根。                                        |
| notoc           | 否      | 值为 `true` 或 `false`。如果为 `true`，则不会为此页面的 HTML 输出生成页内目录。默认为 `false`。适用于某些没有页内标题的着陆页。                      |
| toc_min         | 否      | 如果 `notoc` 设置为 `true` 则忽略。页内目录中包含的最小标题级别。默认为 `2`，表示显示 `<h2>` 标题作为最小级别。                                                           |
| toc_max         | 否      | 如果 `notoc` 设置为 `false` 则忽略。页内目录中包含的最大标题级别。默认为 `3`，表示显示 `<h3>` 标题。设置为与 `toc_min` 相同的值只显示 `toc_min` 级别的标题。  |
| sitemap         | 否      | 从搜索引擎索引中排除页面。当设置为 `false` 时，页面将从 `sitemap.xml` 中排除，并且会向页面添加 `<meta name="robots" content="noindex"/>` 头部。                   |
| sidebar.reverse | 否      | 此部分页面的参数更改该部分中页面的排序顺序。通常按权重或标题排在顶部的页面将出现在底部，反之亦然。 |
| sidebar.goto    | 否      | 设置此项以更改侧边栏应该为此条目指向的 URL。请参见[无页面侧边栏条目](#pageless-sidebar-entries)。                                                                         |
| sidebar.badge   | 否      | 设置此项可为此页面的侧边栏条目添加徽章。此参数选项包含两个字段：`badge.text` 和 `badge.color`。                                                                          |

以下是一个有效（但人为设计的）页面元数据示例。前置信息中元数据元素的顺序并不重要。

```liquid
---
description: 在 Ubuntu 上安装 Docker Engine 的说明
keywords: 要求, apt, 安装, ubuntu, 安装, 卸载, 升级, 更新
title: 在 Ubuntu 上安装 Docker Engine
aliases:
- /ee/docker-ee/ubuntu/
- /engine/installation/linux/docker-ce/ubuntu/
- /engine/installation/linux/docker-ee/ubuntu/
- /engine/installation/linux/ubuntu/
- /engine/installation/linux/ubuntulinux/
- /engine/installation/ubuntulinux/
- /install/linux/docker-ce/ubuntu/
- /install/linux/docker-ee/ubuntu/
- /install/linux/ubuntu/
- /installation/ubuntulinux/
toc_max: 4
---
```

## 正文

页面的正文（除关键词外）在前置信息之后开始。

### 文本长度

分割长行（最好不超过 80 个字符）可以使得对小块文本提供反馈变得更容易。

## 无页面侧边栏条目

如果您想在侧边栏中添加一个条目，但希望链接指向其他地方，您可以使用 `sidebar.goto` 参数。这在与设置为 `always` 的 `build.render` 结合使用时很有用，它会在侧边栏中创建一个链接到另一个页面的无页面条目。

```md
---
title: 虚拟侧边栏链接
build:
  render: never
sidebar:
  goto: /some/other/page/
weight: 30
---
```