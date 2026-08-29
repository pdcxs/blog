---
id: How to create stack project
aliases: []
tags:
  - haskell
  - stack
  - xdg
title: 如何新建 stack 工程
---

一切开始之前，需要搭建好 `Haskell` 开发环境，参考 [[Problems for Haskell Setup|Haskell安装相关问题]]

## stack 加速

参考[清华镜像源](https://mirror.tuna.tsinghua.edu.cn/help/stackage/)中的说明：

### Linux 系统

在 `~/.stack/config.yaml` 中，添加以下内容：

```yaml
setup-info-locations: ["https://mirrors.tuna.tsinghua.edu.cn/stackage/stack-setup.yaml"]
urls:
  latest-snapshot: https://mirrors.tuna.tsinghua.edu.cn/stackage/snapshots.json

snapshot-location-base: https://mirrors.tuna.tsinghua.edu.cn/stackage/stackage-snapshots/
```

最好把 `template` 中的相关参数也填写好，比如：

```yaml
templates:
  params:
    author-name: pdcxs
    author-email: pdcxs@outlook.com
    copyright: MIT
    github-username: pdcxs
```

### Windows 系统

Windows 系统添加的内容是一样的，不过，`config.yaml` 有一些不同，默认情况在 `%APPDATA%\stack\config.yaml` 中，但是，如果设置了 [[XDG Setup|xdg-目录设置|XDG]] 目录，则需要在对应位置查找。参考 [[Problems for Haskell Setup|Haskell安装相关问题]]。

## 新建项目

使用以下命令新建项目：

```bash
stack new projectName
```

新建好后，首先查看系统的 `ghc` 工具链版本：

```bash
ghcup list
```

在选择版本时，需要注意：

1. 选择最新版本的 `hls`
2. 打开 [`hls` 官网](https://haskell-language-server.readthedocs.io/en/latest/support/ghc-version-support.html)，查看支持版本。
3. 打开 [Stackage 官网](https://www.stackage.org/)，查看各个版本的 Stackage 支持的 ghc 的版本。
4. 选择最新支持同时被 `hls` 和 `stackage` 支持的 `ghc` 版本并安装。

比如，我目前的 `hls` 版本最新为 `2.10.0.0`，`hls` 最新版支持的 `ghc` 为 `9.12.2`，但是 `stackage` 最新支持版仅为 `9.10.2`，返回去查看 `9.10.2` 也同时被最新版 `hls` 支持，所以选择了 `9.10.2`。我就安装了对应的版本并设置为默认软件。

```bash
ghcup install hls 2.10.0.0
ghcup set hls 2.10.0.0
ghcup install ghc 9.10.2
ghcup set ghc 9.10.2
```

当然，为了节省空间，建议删除以前的旧版本软件。
运行以下命令则可以清理 `ghcup` 缓存:

```bash
ghcup gc --cache
```

运行以下命令，则可以删除所有非默认软件：

```bash
ghcup gc --unset
```

打开 [清华源 Stackage 目录](https://mirrors.tuna.tsinghua.edu.cn/stackage/stackage-snapshots/)，定位到对应位置，复制其地址。

打开 stack 新建的项目文件中的 `stack.yaml`，更改 `snapshot` 为刚刚复制的内容，例如，我现在最新版本为：
`nightly-2025-05-28`，因此，把 `snapshot` 改为：

```yaml
snapshot:
  url: https://mirrors.tuna.tsinghua.edu.cn/stackage/stackage-snapshots/nightly/2025/5/28.yaml
```

运行 `stack run` 以检查配置正确性。如果正确，应当会编译运行程序，而不会触发多余的下载内容。

## Paths 冲突问题

如果在工程中运行 `stack ghci`，会有如下警告：

```
Warning: Multiple files use the same module name:
         * Paths_XXX found at the following paths
           * .../build/autogen/Paths_XXX.hs
             (XXX:lib)
           * .../build/test-exe/autogen/Paths_XXX.hs
             (XXX:exe:XXX-exe)
```

有以下两种解决办法：

### 指定 `stack` 版本

在 `package.yaml` 中，添加以下内容：

```yaml
spec-version: 0.36.0
```

### 去除 `exe` 路径

修改 `package.yaml` 中的 `executable` 模块，添加 `when` 属性（其他内容不动）：

```yaml
executables:
  XXX-exe:
    main: Main.hs
    source-dirs: app
    ghc-options:
      - -threaded
      - -rtsopts
      - -with-rtsopts=-N
    dependencies:
      - XXX
    when:
      - condition: false
        other-modules: Paths_XXX
```

其中，`XXX` 是对应的工程名称。
