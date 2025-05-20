---
title: 参考文档
linkTitle: 参考
layout: wide
description: 查找 Docker 平台各种 API、CLI 和文件格式的参考文档
params:
  icon: terminal
  notoc: true
  grid_files:
  - title: Dockerfile
    description: 定义单个容器的内容和启动行为。
    icon: edit_document
    link: /reference/dockerfile/
  - title: Compose 文件
    description: 定义多容器应用程序。
    icon: polyline
    link: /reference/compose-file/
  grid_clis:
  - title: Docker CLI
    description: 主要的 Docker 命令行工具，包含所有 `docker` 命令。
    icon: terminal
    link: /reference/cli/docker/
  - title: Compose CLI
    description: Docker Compose 的命令行工具，用于构建和运行多容器应用程序。
    icon: subtitles
    link: /reference/cli/docker/compose/
  - title: 守护进程 CLI (dockerd)
    description: 管理容器的持久进程。
    icon: developer_board
    link: /reference/cli/dockerd/
  grid_apis:
  - title: Engine API
    description: Docker 的主要 API，提供对守护进程的程序化访问。
    icon: api
    link: /reference/api/engine/
  - title: Docker Hub API
    description: 用于与 Docker Hub 交互的 API。
    icon: communities
    link: /reference/api/hub/latest/
  - title: DVP 数据 API
    description: 供 Docker 认证发布者获取分析数据的 API。
    icon: area_chart
    link: /reference/api/hub/dvp/
---

本节包含 Docker 平台的各种 API、CLI、驱动程序、规范和文件格式的参考文档。

## 文件格式

{{< grid items="grid_files" >}}

## 命令行界面 (CLI)

{{< grid items="grid_clis" >}}

## 应用程序编程接口 (API)

{{< grid items="grid_apis" >}}
