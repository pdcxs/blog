---
tags:
  - typst
  - github
---

## 创建内容

自己的模板需要有一个 `github` 仓库，在工程目录下，需要有一个 `typst.toml`，其内容为：

```toml
[package]
name = "example"
version = "0.1.0"
entrypoint = "lib.typ"
authors = ["The Typst Project Developers"]
license = "MIT"
description = "An example package."
```

其中，`name` 项需要和仓库名称、`typst` 的包的名称相同。

如果是一个模板，还需要包括：

```toml
[template]
path = "template"
entrypoint = "main.typ"
thumbnail = "thumbnail.png"
```

并在工程目录下创建 `template` 文件夹，里面的 `main.typ` 为入口程序。工程目录下的 `thumbnail.png` 为模板封面图片，需要高清图片，尽量是模板PDF直接转化出来的图片。

## Fork 官方仓库

打开 [官方 packages 仓库](https://github.com/typst/packages)。

点击 `Fork`，一切选项保持默认。

在初始化 git 目录之前，最好设置默认 `branch` 为 `main`：

```bash
git config --global init.defaultBranch main
```

采用以下命令克隆仓库：

```bash
git clone --depth=1 --filter=blob:none --no-checkout https://github.com/<你的用户名>/packages.git
cd packages
```

注意，`--filter=blob:none` 表示只下载文件结构，不下载实际内容。
`--no-checkout` 表示克隆后不立即检出文件。

接下来启用稀疏检出：

```bash
git sparse-checkout init --cone
```

接下来指定包目录：

```bash
git sparse-checkout set packages/preview/<你的包名>
```

接下来检出文件：

```bash
git checkout
```

注意，如果你之前已经提交过一个 PR 并且已经合并，最好把自己 Fork 的 package 仓库删除，重新 Fork 一个最新版本，这样子提交历史会比较清晰。

在当前目录下，创建目录：`packages/preview/[package-name]/0.1.0/`

将包的所有内容复制到 `0.1.0` 目录下，不要包含 `.git` 目录和生成的 PDF 文件。只包含源代码。

新建 `commit`，commit的内容应该为：`[package-name]:0.1.0`

执行 `git push` 到自己的仓库中。

在网页上打开仓库，点击 `Contribute` -> `Open pull request`，认真阅读并填写模板，提交，等待通过即可。新发布的包可能需要几天的时间才会通过。
