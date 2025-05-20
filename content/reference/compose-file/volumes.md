---
title: 卷顶级元素
description: 探索卷顶级元素可以具有的所有属性。
keywords: compose, compose specification, volumes, compose file reference
aliases: 
 - /compose/compose-file/07-volumes/
weight: 40
---

{{% include "compose/volumes.md" %}}

要在多个服务之间使用卷，您必须通过在 `services` 顶级元素中使用 [volumes](services.md#volumes) 属性显式授予每个服务访问权限。`volumes` 属性具有额外的语法，提供更细粒度的控制。

> [!TIP]
>
> 正在处理大型仓库或单体仓库，或者虚拟文件系统不再随代码库扩展？
> Compose 现在利用[同步文件共享](/manuals/desktop/features/synchronized-file-sharing.md)并自动为绑定挂载创建文件共享。
> 确保您已使用付费订阅登录 Docker，并在 Docker Desktop 的设置中启用了**访问实验性功能**和**使用 Compose 管理同步文件共享**。

## 示例

以下示例显示了一个双服务设置，其中数据库的数据目录作为名为 `db-data` 的卷与另一个服务共享，以便可以定期备份。

```yml
services:
  backend:
    image: example/database
    volumes:
      - db-data:/etc/data

  backup:
    image: backup-service
    volumes:
      - db-data:/var/lib/backup/data

volumes:
  db-data:
```

`db-data` 卷分别挂载在备份和后端的 `/var/lib/backup/data` 和 `/etc/data` 容器路径上。

运行 `docker compose up` 会在卷不存在时创建卷。否则，将使用现有卷，如果在 Compose 之外手动删除，则会重新创建。

## 属性

顶级 `volumes` 部分下的条目可以为空，在这种情况下，它使用容器引擎的默认配置来创建卷。或者，您可以使用以下键配置它：

### `driver`

指定应使用哪个卷驱动程序。如果驱动程序不可用，Compose 将返回错误并且不部署应用程序。

```yml
volumes:
  db-data:
    driver: foobar
```

### `driver_opts`

`driver_opts` 指定要传递给驱动程序的键值对选项列表。这些选项取决于驱动程序。

```yml
volumes:
  example:
    driver_opts:
      type: "nfs"
      o: "addr=10.40.0.199,nolock,soft,rw"
      device: ":/docker/example"
```

### `external`

如果设置为 `true`：
 - `external` 指定此卷已存在于平台上，其生命周期在应用程序之外管理。
   Compose 不会创建卷，如果卷不存在则返回错误。
 - 除了 `name` 之外的所有其他属性都无关紧要。如果 Compose 检测到任何其他属性，它会将 Compose 文件视为无效。

在以下示例中，Compose 不会尝试创建名为 `{project_name}_db-data` 的卷，而是查找名为 `db-data` 的现有卷并将其挂载到 `backend` 服务的容器中。

```yml
services:
  backend:
    image: example/database
    volumes:
      - db-data:/etc/data

volumes:
  db-data:
    external: true
```

### `labels`

`labels` 用于向卷添加元数据。您可以使用数组或字典。

建议使用反向 DNS 表示法，以防止您的标签与其他软件使用的标签冲突。

```yml
volumes:
  db-data:
    labels:
      com.example.description: "Database volume"
      com.example.department: "IT/Ops"
      com.example.label-with-empty-value: ""
```

```yml
volumes:
  db-data:
    labels:
      - "com.example.description=Database volume"
      - "com.example.department=IT/Ops"
      - "com.example.label-with-empty-value"
```

Compose 设置 `com.docker.compose.project` 和 `com.docker.compose.volume` 标签。

### `name`

`name` 为卷设置自定义名称。name 字段可用于引用包含特殊字符的卷。名称按原样使用，不包含堆栈名称。

```yml
volumes:
  db-data:
    name: "my-app-data"
```

这使得可以将此查找名称作为 Compose 文件的参数，以便卷的模型 ID 是硬编码的，但平台上的实际卷 ID 在部署时在运行时设置。

例如，如果 `.env` 文件中有 `DATABASE_VOLUME=my_volume_001`：

```yml
volumes:
  db-data:
    name: ${DATABASE_VOLUME}
```

运行 `docker compose up` 使用名为 `my_volume_001` 的卷。

它也可以与 `external` 属性一起使用。这意味着用于在平台上查找实际卷的名称与用于在 Compose 文件中引用卷的名称是分开设置的：

```yml
volumes:
  db-data:
    external: true
    name: actual-name-of-volume
```
