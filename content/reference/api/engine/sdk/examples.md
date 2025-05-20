---
title: SDK 示例
linkTitle: 示例
description: 学习如何使用 Docker Engine SDK 来开发您的应用程序。这些示例涵盖了
  常见的使用场景。
keywords: sdk, docker, examples, golang, python
aliases:
  - /develop/sdk/examples/
  - /engine/api/get-started/
  - /engine/api/sdk/examples/
  - /engine/api/sdk/
  - /engine/reference/api/remote_api_client_libraries/
  - /engine/reference/api/sdk/examples/
---

本页包含了一些使用 Docker Engine SDKs 的示例代码。这些例子展示了
如何使用 SDK 来创建，管理和删除 Docker 资源，如容器，镜像和卷。
这些例子不会涵盖所有的 SDK 功能，完整的 API 参考文档可在 
[Go](https://godoc.org/github.com/docker/docker/client) 或
[Python](https://docker-py.readthedocs.io/en/stable/) 中找到。

## 运行容器

这个例子演示了如何使用 SDK 创建并启动一个容器。该容器运行一个命令，
然后立即退出。SDK 还支持使用命令的 stdin, stdout, stderr 流。

{{< tabs >}}
{{< tab name="Go" >}}

这个例子创建一个 Docker 客户端，拉取 Alpine 镜像，创建一个容器并在
容器中运行 "echo" 命令。

```go
package main

import (
    "context"
    "io"
    "os"

    "github.com/docker/docker/api/types/container"
    "github.com/docker/docker/api/types/image"
    "github.com/docker/docker/client"
    "github.com/docker/docker/pkg/stdcopy"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    reader, err := cli.ImagePull(ctx, "docker.io/library/alpine", image.PullOptions{})
    if err != nil {
        panic(err)
    }
    io.Copy(os.Stdout, reader)

    resp, err := cli.ContainerCreate(ctx, &container.Config{
        Image: "alpine",
        Cmd:   []string{"echo", "hello world"},
    }, nil, nil, nil, "")
    if err != nil {
        panic(err)
    }

    if err := cli.ContainerStart(ctx, resp.ID, container.StartOptions{}); err != nil {
        panic(err)
    }

    statusCh, errCh := cli.ContainerWait(ctx, resp.ID, container.WaitConditionNotRunning)
    select {
    case err := <-errCh:
        if err != nil {
            panic(err)
        }
    case <-statusCh:
    }

    out, err := cli.ContainerLogs(ctx, resp.ID, container.LogsOptions{ShowStdout: true})
    if err != nil {
        panic(err)
    }

    stdcopy.StdCopy(os.Stdout, os.Stderr, out)
}
```

{{< /tab >}}
{{< tab name="Python" >}}

这个例子使用 Python SDK 实现了相同的功能。

```python
import docker
client = docker.from_env()
print(client.containers.run("alpine", ["echo", "hello world"]))
```

{{< /tab >}}
{{< /tabs >}}

## 在后台运行容器

这个例子演示了如何使用 SDK 在后台启动容器。Docker 引擎会一直运行容器，
直到容器内主进程退出。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "fmt"

    "github.com/docker/docker/api/types/container"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    resp, err := cli.ContainerCreate(ctx, &container.Config{
        Image: "ubuntu:latest",
        Cmd:   []string{"sleep", "infinity"},
    }, nil, nil, nil, "")
    if err != nil {
        panic(err)
    }

    if err := cli.ContainerStart(ctx, resp.ID, container.StartOptions{}); err != nil {
        panic(err)
    }

    fmt.Printf("容器 ID: %s\n", resp.ID)
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
container = client.containers.run("ubuntu:latest", ["sleep", "infinity"], detach=True)
print(container.id)
```

{{< /tab >}}
{{< /tabs >}}

## 列出和管理容器

这个例子展示了如何列出正在运行的容器，并执行一些基本的容器管理操作。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "fmt"

    "github.com/docker/docker/api/types"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    containers, err := cli.ContainerList(ctx, types.ContainerListOptions{})
    if err != nil {
        panic(err)
    }

    for _, container := range containers {
        fmt.Printf("%s %s\n", container.ID[:10], container.Image)
    }
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
for container in client.containers.list():
    print(container.id)
```

{{< /tab >}}
{{< /tabs >}}

## 停止所有运行中的容器

这个例子展示了如何停止所有正在运行的容器。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"

    "github.com/docker/docker/api/types"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    containers, err := cli.ContainerList(ctx, types.ContainerListOptions{})
    if err != nil {
        panic(err)
    }

    for _, container := range containers {
        if err := cli.ContainerStop(ctx, container.ID, container.StopOptions); err != nil {
            panic(err)
        }
    }
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
for container in client.containers.list():
    container.stop()
```

{{< /tab >}}
{{< /tabs >}}

## 打印指定容器的日志

这个例子展示了如何打印一个容器的日志。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "io"
    "os"

    "github.com/docker/docker/api/types"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    options := types.ContainerLogsOptions{ShowStdout: true}
    out, err := cli.ContainerLogs(ctx, "container_id", options)
    if err != nil {
        panic(err)
    }

    io.Copy(os.Stdout, out)
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
container = client.containers.get("container_id")
print(container.logs())
```

{{< /tab >}}
{{< /tabs >}}

## 列出所有镜像

这个例子展示了如何列出系统中所有的 Docker 镜像。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "fmt"

    "github.com/docker/docker/api/types"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    images, err := cli.ImageList(ctx, types.ImageListOptions{})
    if err != nil {
        panic(err)
    }

    for _, image := range images {
        fmt.Printf("%s\n", image.ID)
    }
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
for image in client.images.list():
    print(image.id)
```

{{< /tab >}}
{{< /tabs >}}

## 拉取镜像

这个例子展示了如何从 Docker Hub 或其他 registry 拉取镜像。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "io"
    "os"

    "github.com/docker/docker/api/types/image"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    reader, err := cli.ImagePull(ctx, "alpine:latest", image.PullOptions{})
    if err != nil {
        panic(err)
    }
    io.Copy(os.Stdout, reader)
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
image = client.images.pull("alpine:latest")
print(image.id)
```

{{< /tab >}}
{{< /tabs >}}

## 带认证信息拉取镜像

这个例子展示了如何使用认证信息从私有 registry 拉取镜像。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "encoding/base64"
    "encoding/json"
    "io"
    "os"

    "github.com/docker/docker/api/types"
    "github.com/docker/docker/api/types/image"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    authConfig := types.AuthConfig{
        Username: "username",
        Password: "password",
    }
    encodedJSON, err := json.Marshal(authConfig)
    if err != nil {
        panic(err)
    }
    authStr := base64.URLEncoding.EncodeToString(encodedJSON)

    reader, err := cli.ImagePull(ctx, "private-registry.io/username/alpine", image.PullOptions{
        RegistryAuth: authStr,
    })
    if err != nil {
        panic(err)
    }
    io.Copy(os.Stdout, reader)
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
client.login(username='username', password='password')
image = client.images.pull('private-registry.io/username/alpine')
print(image.id)
```

{{< /tab >}}
{{< /tabs >}}

## 提交容器更改

这个例子展示了如何将容器的更改保存为新的镜像。这与 `docker commit` 命令类似。

{{< tabs >}}
{{< tab name="Go" >}}

```go
package main

import (
    "context"
    "fmt"

    "github.com/docker/docker/api/types/container"
    "github.com/docker/docker/api/types/image"
    "github.com/docker/docker/client"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }
    defer cli.Close()

    createResp, err := cli.ContainerCreate(ctx, &container.Config{
        Image: "alpine",
        Cmd:   []string{"touch", "/helloworld"},
    }, nil, nil, nil, "")
    if err != nil {
        panic(err)
    }

    if err := cli.ContainerStart(ctx, createResp.ID, container.StartOptions{}); err != nil {
        panic(err)
    }

    statusCh, errCh := cli.ContainerWait(ctx, createResp.ID, container.WaitConditionNotRunning)
    select {
    case err := <-errCh:
        if err != nil {
            panic(err)
        }
    case <-statusCh:
    }

    commitResp, err := cli.ContainerCommit(ctx, createResp.ID, image.CommitOptions{
        Reference: "helloworld",
    })
    if err != nil {
        panic(err)
    }

    fmt.Println(commitResp.ID)
}
```

{{< /tab >}}
{{< tab name="Python" >}}

```python
import docker
client = docker.from_env()
container = client.containers.run("alpine", ["touch", "/helloworld"], detach=True)
container.wait()
image = container.commit("helloworld")
print(image.id)
```

{{< /tab >}}
{{< /tabs >}}

这些示例展示了 Docker Engine SDK 的一些基本用法。要了解更多功能，请参考
[Go SDK](https://godoc.org/github.com/docker/docker/client) 或
[Python SDK](https://docker-py.readthedocs.io/en/stable/) 的完整文档。