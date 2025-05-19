---
description: Docker 文档中使用的组件和格式示例
title: 选项卡
toc_max: 3
---

选项卡组件由两个短代码组成：

- `{{</* tabs */>}}`
- `{{</* tab name="选项卡名称" */>}}`

`{{</* tabs */>}}` 短代码是一个父组件，包含多个 `tab`。
每个 `{{</* tab */>}}` 使用 `name` 属性指定名称。

您可以为 `tabs` 包装器指定一个可选的 `group` 属性，表示
选项卡部分应属于一组选项卡。请参阅[分组](#分组)。

## 示例

{{< tabs >}}
{{< tab name="JavaScript">}}

```js
console.log("你好，世界")
```

{{< /tab >}}
{{< tab name="Go">}}

```go
fmt.Println("你好，世界")
```

{{< /tab >}}
{{< /tabs >}}

## 标记语法

````markdown
{{</* tabs */>}}
{{</* tab name="JavaScript" */>}}

```js
console.log("你好，世界")
```

{{</* /tab */>}}
{{</* tab name="Go" */>}}

```go
fmt.Println("你好，世界")
```

{{</* /tab */>}}
{{</* /tabs */>}}
````

## 分组

您可以在 `tabs` 短代码上指定一个可选的选项卡组。
这样做将同步属于同一组的所有选项卡的选择状态。

### 选项卡组示例

以下示例展示了两个属于同一组的选项卡部分。

{{< tabs group="code" >}}
{{< tab name="JavaScript">}}

```js
console.log("你好，世界")
```

{{< /tab >}}
{{< tab name="Go">}}

```go
fmt.Println("你好，世界")
```

{{< /tab >}}
{{< /tabs >}}

{{< tabs group="code" >}}
{{< tab name="JavaScript">}}

```js
const res = await fetch("/users/1")
```

{{< /tab >}}
{{< tab name="Go">}}

```go
resp, err := http.Get("/users/1")
```

{{< /tab >}}
{{< /tabs >}}