# 为 Docker 文档做贡献

我们非常重视来自 Docker 社区的文档贡献。我们希望尽可能地让您在这个代码仓库中工作变得简单方便。

我们的风格指南以及如何使用页面模板和组件的说明可在网站的[贡献部分](https://docs.docker.com/contribute/)中找到。

以下指南描述了您可以为 <https://docs.docker.com/> 上的 Docker 文档做出贡献的方式，以及如何开始。

[English](CONTRIBUTING.md)

## 报告问题

如果您遇到内容问题或网站整体问题，欢迎在我们的 [GitHub 问题追踪器](https://github.com/docker/docs/issues)中[提交问题](https://github.com/docker/docs/issues/new/choose)。您也可以使用问题追踪器提出改进建议，或者建议您认为缺失或想要看到的新内容。

## 编辑内容

该网站使用 [Hugo](https://gohugo.io/) 构建。内容主要是本仓库 `/content` 目录中的 Markdown 文件（有少数例外，请参见[此处未编辑的内容](#此处未编辑的内容)）。

网站侧边栏导航的结构由 `contents` 目录中的站点部分层次结构定义。页面的标题在 Markdown 文件的前置元数据中定义。您可以使用 `title` 和 `linkTitle` 来定义页面标题。`title` 用于页面标题，`linkTitle` 用于侧边栏标题。如果未定义 `linkTitle`，则两处都使用 `title`。

您必须先 fork 本仓库才能创建 pull request 来提出更改。更多详情，请参见[本地设置](#本地设置)。

### 一般准则

遵循以下准则可以帮助简化审查过程：

- 尽可能避免在单个 PR 中修改大量文件。
- 除非必要，否则不要更改您未编辑部分的空白符或换行。确保您的文本编辑器没有配置为保存时自动重新格式化整个文件。
- 我们使用 GitHub Actions 进行测试，并为每个 pull request 创建预览部署。预览部署的 URL 会作为评论添加到 pull request 中。检查预览站点以验证您的更改效果，并在必要时修复问题。

### 本地设置

您可以使用 Docker（惊喜）在本地构建和提供文件服务。

> [!重要]
> 这需要 Docker Desktop **4.24** 或更高版本，或带有 Docker Compose [**2.22**](https://docs.docker.com/compose/how-tos/file-watch/) 或更高版本的 Docker Engine。

1. [Fork docker/docs 仓库。](https://github.com/docker/docs/fork)

2. 克隆您 fork 的文档仓库：

   ```console
   $ git clone https://github.com/<your-username>/docs
   $ cd docs
   ```

3. 配置 Git 以同步您的文档 fork 与上游 docker/docs 仓库，并防止意外推送到上游仓库：

   ```console
   $ git remote add upstream https://github.com/docker/docs.git
   $ git remote set-url --push upstream no_pushing
   ```

4. 检出分支：

   ```console
   $ git checkout -b <branch>
   ```

5. 启动本地开发服务器：

   ```console
   $ docker compose watch
   ```

该站点将在 <http://localhost:1313> 提供本地预览。开发服务器会监视更改并自动重建您的站点。

要停止开发服务器：

1. 在终端中，按 `<Ctrl+C>` 退出 Compose 的文件监视模式。
2. 使用 `docker compose down` 命令停止 Compose 服务。

### 测试

在推送更改并开启 pull request 之前，我们建议您先在本地测试您的站点。本地测试会检查断开的链接、格式不正确的标记和其他问题。要运行测试：

```console
$ docker buildx bake validate
```

如果此命令没有产生任何错误，那就说明一切正常！

## 此处未编辑的内容

CLI 参考文档在上游仓库中维护。它部分是由代码生成的，这里只是用于发布。要更新 CLI 参考文档，请参考相应的仓库：

- [docker/cli](https://github.com/docker/cli)
- [docker/buildx](https://github.com/docker/buildx)
- [docker/compose](https://github.com/docker/compose)

如果您不确定如何进行，请随时在此仓库中提出问题，我们会提供帮助。

其他出现在网站上但不在此处编辑的内容包括：

- Dockerfile 参考
- Docker Engine API 参考
- Compose 规范
- Buildx Bake 参考

如果您在这些页面中发现问题，请随时在此处提出问题，我们会确保在上游源代码中修复它。