---
title: Compose 部署规范
description: 了解 Compose 部署规范
keywords: compose, compose specification, compose file reference, compose deploy specification
aliases: 
 - /compose/compose-file/deploy/
weight: 140
---

{{% include "compose/deploy.md" %}}

## 属性

### `endpoint_mode`

`endpoint_mode` 指定外部客户端连接到服务的服务发现方法。Compose 部署规范定义了两个规范值：

* `endpoint_mode: vip`：为服务分配一个虚拟 IP（VIP），作为客户端在网络上访问服务的前端。平台在客户端和运行服务的节点之间路由请求，客户端无需知道有多少节点参与服务或其 IP 地址或端口。

* `endpoint_mode: dnsrr`：平台为服务设置 DNS 条目，使得对服务名称的 DNS 查询返回 IP 地址列表（DNS 轮询），客户端直接连接到其中一个。

```yml
services:
  frontend:
    image: example/webapp
    ports:
      - "8080:80"
    deploy:
      mode: replicated
      replicas: 2
      endpoint_mode: vip
```

### `labels`

`labels` 指定服务的元数据。这些标签仅设置在服务上，而不是服务的任何容器上。这假设平台具有某种可以匹配 Compose 应用程序模型的"服务"原生概念。

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      labels:
        com.example.description: "This label will appear on the web service"
```

### `mode`

`mode` 定义用于运行服务或作业的复制模型。选项包括：

- `global`：确保每个物理节点上恰好有一个任务持续运行，直到停止。
- `replicated`：在节点上持续运行指定数量的任务，直到停止（默认）。
- `replicated-job`：执行定义数量的任务，直到达到完成状态（以代码 0 退出）。
   - 总任务数由 `replicas` 确定。
   - 可以使用 `max-concurrent` 选项限制并发性（仅限 CLI）。
- `global-job`：在每个物理节点上执行一个任务，直到达到完成状态（以代码 0 退出）。
   - 在添加新节点时自动运行。

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      mode: global

  batch-job:
    image: example/processor
    deploy:
      mode: replicated-job
      replicas: 5

  maintenance:
    image: example/updater
    deploy:
      mode: global-job
```

> [!NOTE] 
> - 作业模式（`replicated-job` 和 `global-job`）专为完成并以代码 0 退出的任务设计。
> - 已完成的任务会保留，直到明确删除。
> - 控制并发性的选项（如 `max-concurrent`）仅通过 CLI 支持，在 Compose 中不可用。

有关作业选项和行为的更详细信息，请参阅 [Docker CLI 文档](/reference/cli/docker/service/create.md#running-as-a-job)

### `placement`

`placement` 指定平台选择物理节点运行服务容器的约束和偏好。

#### `constraints`

`constraints` 定义平台节点必须满足才能运行服务容器的必需属性。有关更多示例，请参阅 [CLI 参考文档](/reference/cli/docker/service/create.md#constraint)。

```yml
deploy:
  placement:
    constraints:
      - disktype=ssd
```

#### `preferences`

`preferences` 定义一种策略（目前仅支持 `spread` 策略）来在数据中心节点标签的值上均匀分布任务。有关更多示例，请参阅 [CLI 参考文档](/reference/cli/docker/service/create.md#placement-pref)

```yml
deploy:
  placement:
    preferences:
      - spread: node.labels.zone
```

### `replicas`

如果服务是 `replicated`（这是默认值），`replicas` 指定在任何给定时间应该运行的容器数量。

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      mode: replicated
      replicas: 6
```

### `resources`

`resources` 配置容器在平台上运行的物理资源约束。这些约束可以配置为：

- `limits`：平台必须防止容器分配更多资源。
- `reservations`：平台必须保证容器至少可以分配配置的数量。

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
          pids: 1
        reservations:
          cpus: '0.25'
          memory: 20M
```

#### `cpus`

`cpus` 配置容器可以使用的可用 CPU 资源的限制或保留，以核心数表示。

#### `memory`

`memory` 配置容器可以分配的内存量限制或保留，设置为表示[字节值](extension.md#specifying-byte-values)的字符串。

#### `pids`

`pids` 调整容器的 PIDs 限制，设置为整数。

#### `devices`

`devices` 配置容器可以使用的设备保留。它包含保留列表，每个保留都设置为具有以下参数的对象：`capabilities`、`driver`、`count`、`device_ids` 和 `options`。

设备使用功能列表进行保留，使 `capabilities` 成为唯一必需的字段。设备必须满足所有请求的功能才能成功保留。

##### `capabilities`

`capabilities` 设置为字符串列表，表示通用和驱动程序特定的功能。
目前识别以下通用功能：

- `gpu`：图形加速器
- `tpu`：AI 加速器

为避免名称冲突，驱动程序特定的功能必须以驱动程序名称作为前缀。
例如，保留 NVIDIA CUDA 加速器可能如下所示：

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["nvidia-compute"]
```

##### `driver`

可以使用 `driver` 字段请求保留设备的其他驱动程序。值指定为字符串。

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["nvidia-compute"]
          driver: nvidia
```

##### `count`

如果 `count` 设置为 `all` 或未指定，Compose 保留满足请求功能的所有设备。否则，Compose 至少保留指定数量的设备。值指定为整数。

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["tpu"]
          count: 2
```

`count` 和 `device_ids` 字段是互斥的。如果同时指定两者，Compose 将返回错误。

##### `device_ids`

如果设置了 `device_ids`，Compose 保留具有指定 ID 的设备，前提是它们满足请求的功能。值指定为字符串列表。

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["gpu"]
          device_ids: ["GPU-f123d1c9-26bb-df9b-1c23-4a731f61d8c7"]
```

`count` 和 `device_ids` 字段是互斥的。如果同时指定两者，Compose 将返回错误。

##### `options`

可以使用 `options` 设置驱动程序特定的选项作为键值对。

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["gpu"]
          driver: gpuvendor
          options:
            virtualization: false
```

### `restart_policy`

`restart_policy` 配置容器退出时是否以及如何重启。如果未设置 `restart_policy`，Compose 会考虑服务配置设置的 `restart` 字段。

- `condition`。当设置为：
  - `none`，无论退出状态如何，容器都不会自动重启。
  - `on-failure`，如果容器因错误退出（表现为非零退出代码），则重启容器。
  - `any`（默认），无论退出状态如何，容器都会重启。
- `delay`：重启尝试之间等待的时间，指定为[持续时间](extension.md#specifying-durations)。默认为 0，意味着可以立即进行重启尝试。
- `max_attempts`：在放弃之前允许的最大失败重启尝试次数。（默认：无限重试。）
  只有在 `window` 定义的时间内容器未能成功重启时，失败的尝试才会计入 `max_attempts`。
  例如，如果 `max_attempts` 设置为 `2`，并且容器在第一次尝试时未能在窗口内重启，Compose 会继续重试，直到发生两次这样的失败尝试，即使这意味着尝试超过两次。
- `window`：重启后等待以确定是否成功的时间量，指定为[持续时间](extension.md#specifying-durations)（默认：重启后立即评估结果）。

```yml
deploy:
  restart_policy:
    condition: on-failure
    delay: 5s
    max_attempts: 3
    window: 120s
```

### `rollback_config`

`rollback_config` 配置在更新失败时如何回滚服务。

- `parallelism`：同时回滚的容器数量。如果设置为 0，所有容器同时回滚。
- `delay`：每个容器组回滚之间等待的时间（默认 0s）。
- `failure_action`：如果回滚失败该怎么办。`continue` 或 `pause` 之一（默认 `pause`）。
- `monitor`：每次任务更新后监控失败的时间 `(ns|us|ms|s|m|h)`（默认 0s）。
- `max_failure_ratio`：回滚期间可容忍的失败率（默认 0）。
- `order`：回滚期间的操作顺序。`stop-first`（在启动新任务之前停止旧任务）或 `start-first`（先启动新任务，运行的任务短暂重叠）之一（默认 `stop-first`）。

### `update_config`

`update_config` 配置如何更新服务。用于配置滚动更新。

- `parallelism`：同时更新的容器数量。
- `delay`：更新一组容器之间等待的时间。
- `failure_action`：如果更新失败该怎么办。`continue`、`rollback` 或 `pause` 之一（默认：`pause`）。
- `monitor`：每次任务更新后监控失败的时间 `(ns|us|ms|s|m|h)`（默认 0s）。
- `max_failure_ratio`：更新期间可容忍的失败率。
- `order`：更新期间的操作顺序。`stop-first`（在启动新任务之前停止旧任务）或 `start-first`（先启动新任务，运行的任务短暂重叠）之一（默认 `stop-first`）。

```yml
deploy:
  update_config:
    parallelism: 2
    delay: 10s
    order: stop-first
```
