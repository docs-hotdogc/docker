---
description: Docker 文档中使用的组件和格式示例
title: 徽章
toc_max: 3
---

### 示例

{{< badge color=blue text="蓝色徽章" >}}
{{< badge color=amber text="琥珀色徽章" >}}
{{< badge color=red text="红色徽章" >}}
{{< badge color=green text="绿色徽章" >}}
{{< badge color=violet text="紫色徽章" >}}

您也可以将徽章设为链接。

[{{< badge color="blue" text="带链接的徽章" >}}](../_index.md)

### 使用指南

我们使用徽章来标识处于发布生命周期不同阶段的新内容和产品内容：

- 使用紫色徽章突出显示新的早期访问或实验性内容
- 使用蓝色徽章突出显示测试版内容
- 使用绿色徽章突出显示已正式发布的内容或非产品相关的内容，如指南/学习路径

最佳实践是在功能发布后不超过 2 个月使用此徽章。

### 标记语法

```go
{{</* badge color=amber text="琥珀色徽章" */>}}
[{{</* badge color="blue" text="带链接的徽章" */>}}](../overview.md)
```