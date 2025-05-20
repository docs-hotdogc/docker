---
title: "接口: BackendV0"
description: Docker 扩展 API 参考文档
keywords: Docker, 扩展, sdk, API, 参考
aliases:
 - /desktop/extensions-sdk/dev/api/reference/interfaces/BackendV0/
 - /extensions/extensions-sdk/dev/api/reference/interfaces/BackendV0/
---

## 容器方法

### execInContainer

▸ **execInContainer**(`container`, `cmd`): `Promise`<[`ExecResultV0`](ExecResultV0.md)\>

在容器内执行命令。

```typescript
const output = await window.ddClient.backend.execInContainer(container, cmd);

console.log(output);
```

> [!WARNING]
>
> 此方法将在未来版本中移除。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `container` | `string` | 容器标识符 |
| `cmd` | `string` | 要执行的命令 |

#### 返回值

`Promise`<[`ExecResultV0`](ExecResultV0.md)\>

___

## HTTP 方法

### get

▸ **get**(`url`): `Promise`<`unknown`\>

向后端服务发送 HTTP GET 请求。

```typescript
window.ddClient.backend
 .get("/some/service")
 .then((value: any) => console.log(value));
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [get](HttpService.md#get) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `url` | `string` | 后端服务的 URL |

#### 返回值

`Promise`<`unknown`\>

___

### post

▸ **post**(`url`, `data`): `Promise`<`unknown`\>

向后端服务发送 HTTP POST 请求。

```typescript
window.ddClient.backend
 .post("/some/service", { ... })
 .then((value: any) => console.log(value));
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [post](HttpService.md#post) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `url` | `string` | 后端服务的 URL |
| `data` | `any` | 请求体数据 |

#### 返回值

`Promise`<`unknown`\>

___

### put

▸ **put**(`url`, `data`): `Promise`<`unknown`\>

向后端服务发送 HTTP PUT 请求。

```typescript
window.ddClient.backend
 .put("/some/service", { ... })
 .then((value: any) => console.log(value));
```

> [!WARNING]
> 
> 此方法将在未来版本中移除。请使用 [put](HttpService.md#put) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `url` | `string` | 后端服务的 URL |
| `data` | `any` | 请求体数据 |

#### 返回值

`Promise`<`unknown`\>

___

### patch

▸ **patch**(`url`, `data`): `Promise`<`unknown`\>

向后端服务发送 HTTP PATCH 请求。

```typescript
window.ddClient.backend
 .patch("/some/service", { ... })
 .then((value: any) => console.log(value));
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [patch](HttpService.md#patch) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `url` | `string` | 后端服务的 URL |
| `data` | `any` | 请求体数据 |

#### 返回值

`Promise`<`unknown`\>

___

### delete

▸ **delete**(`url`): `Promise`<`unknown`\>

向后端服务发送 HTTP DELETE 请求。

```typescript
window.ddClient.backend
 .delete("/some/service")
 .then((value: any) => console.log(value));
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [delete](HttpService.md#delete) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `url` | `string` | 后端服务的 URL |

#### 返回值

`Promise`<`unknown`\>

___

### head

▸ **head**(`url`): `Promise`<`unknown`\>

向后端服务发送 HTTP HEAD 请求。

```typescript
window.ddClient.backend
 .head("/some/service")
 .then((value: any) => console.log(value));
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [head](HttpService.md#head) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `url` | `string` | 后端服务的 URL |

#### 返回值

`Promise`<`unknown`\>

___

### request

▸ **request**(`config`): `Promise`<`unknown`\>

向后端服务发送 HTTP 请求。

```typescript
window.ddClient.backend
 .request({ url: "/url", method: "GET", headers: { 'header-key': 'header-value' }, data: { ... }})
 .then((value: any) => console.log(value));
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [request](HttpService.md#request) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `config` | [`RequestConfigV0`](RequestConfigV0.md) | HTTP 请求配置 |

#### 返回值

`Promise`<`unknown`\>

___

## 虚拟机方法

### execInVMExtension

▸ **execInVMExtension**(`cmd`): `Promise`<[`ExecResultV0`](ExecResultV0.md)\>

在后端容器内执行命令。
如果您的扩展包含需要在后端容器中运行的额外二进制文件，您可以使用 `execInVMExtension` 函数。

```typescript
const output = await window.ddClient.backend.execInVMExtension(
  `cliShippedInTheVm xxx`
);

console.log(output);
```

> [!WARNING]
>
> 此方法将在未来版本中移除。请使用 [exec](ExtensionCli.md#exec) 代替。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `cmd` | `string` | 要执行的命令 |

#### 返回值

`Promise`<[`ExecResultV0`](ExecResultV0.md)\>

___

### spawnInVMExtension

▸ **spawnInVMExtension**(`cmd`, `args`, `callback`): `void`

在后端容器中执行命令并返回命令的流。

```typescript
window.ddClient.spawnInVMExtension(
  `cmd`,
  [`arg1`, `arg2`],
  (data: any, err: any) => {
    console.log(data.stdout, data.stderr);
    // 命令退出时我们会得到状态码
    if (data.code) {
      console.log(data.code);
    }
  }
);
```

> [!WARNING]
>
> 此方法将在未来版本中移除。

#### 参数

| 名称 | 类型 | 描述 |
| :------ | :------ | :------ |
| `cmd` | `string` | 要执行的命令 |
| `args` | `string`[] | 要执行的命令的参数 |
| `callback` | (`data`: `any`, `error`: `any`) => `void` | 用于监听命令输出数据和错误的回调函数 |

#### 返回值

`void`