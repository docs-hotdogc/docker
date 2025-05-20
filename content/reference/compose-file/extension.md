---
title: 扩展
description: 了解如何使用扩展
keywords: compose, compose specification, extensions, compose file reference
aliases: 
 - /compose/compose-file/11-extension/
weight: 80
---

{{% include "compose/extension.md" %}}

扩展也可以与[锚点和别名](fragments.md)一起使用。

它们还可以在 Compose 文件中的任何结构中用于不需要用户定义键的地方。
Compose 使用这些来启用实验性功能，就像浏览器添加对[自定义 CSS 功能](https://www.w3.org/TR/2011/REC-CSS2-20110607/syndata.html#vendor-keywords)的支持一样。

## 示例 1

```yml
x-custom:
  foo:
    - bar
    - zot

services:
  webapp:
    image: example/webapp
    x-foo: bar
```

```yml
service:
  backend:
    deploy:
      placement:
        x-aws-role: "arn:aws:iam::XXXXXXXXXXXX:role/foo"
        x-aws-region: "eu-west-3"
        x-azure-region: "france-central"
```

## 示例 2

```yml
x-env: &env
  environment:
    - CONFIG_KEY
    - EXAMPLE_KEY
 
services:
  first:
    <<: *env
    image: my-image:latest
  second:
    <<: *env
    image: another-image:latest
```

在此示例中，环境变量不属于任何服务。它们已完全提升到 `x-env` 扩展字段中。
这定义了一个包含环境字段的新节点。使用 `&env` YAML 锚点，以便两个服务都可以将扩展字段的值引用为 `*env`。

## 示例 3

```yml
x-function: &function
 labels:
   function: "true"
 depends_on:
   - gateway
 networks:
   - functions
 deploy:
   placement:
     constraints:
       - 'node.platform.os == linux'
services:
 # Node.js 提供有关节点（主机）的 OS 信息
 nodeinfo:
   <<: *function
   image: functions/nodeinfo:latest
   environment:
     no_proxy: "gateway"
     https_proxy: $https_proxy
 # 使用 `cat` 回显响应，执行最快的函数。
 echoit:
   <<: *function
   image: functions/alpine:health
   environment:
     fprocess: "cat"
     no_proxy: "gateway"
     https_proxy: $https_proxy
```

`nodeinfo` 和 `echoit` 服务都通过 `&function` 锚点包含 `x-function` 扩展，然后设置其特定的镜像和环境。

## 示例 4

使用 [YAML 合并](https://yaml.org/type/merge.html)，还可以使用多个扩展并根据特定需求共享和覆盖其他属性：

```yml
x-environment: &default-environment
  FOO: BAR
  ZOT: QUIX
x-keys: &keys
  KEY: VALUE
services:
  frontend:
    image: example/webapp
    environment: 
      << : [*default-environment, *keys]
      YET_ANOTHER: VARIABLE
```

> [!NOTE]
>
> [YAML 合并](https://yaml.org/type/merge.html)仅适用于映射，不能用于序列。
>
> 在上面的示例中，环境变量使用 `FOO: BAR` 映射语法声明，而序列语法 `- FOO=BAR` 仅在未涉及片段时有效。

## 信息性历史注释

本节仅供参考。在编写时，已知存在以下前缀：

| 前缀       | 供应商/组织    |
| ---------- | -------------- |
| docker     | Docker         |
| kubernetes | Kubernetes     |

## 指定字节值

值以 `{amount}{byte unit}` 格式的字符串表示字节值：
支持的单位是 `b`（字节）、`k` 或 `kb`（千字节）、`m` 或 `mb`（兆字节）和 `g` 或 `gb`（吉字节）。

```text
    2b
    1024kb
    2048k
    300m
    1gb
```

## 指定持续时间

值以 `{value}{unit}` 形式的字符串表示持续时间。
支持的单位是 `us`（微秒）、`ms`（毫秒）、`s`（秒）、`m`（分钟）和 `h`（小时）。
值可以组合多个值，无需分隔符。

```text
  10ms
  40s
  1m30s
  1h5m30s20ms
```
