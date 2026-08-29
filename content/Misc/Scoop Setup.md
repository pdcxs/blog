---
id: Scoop Setup
aliases: []
tags:
  - scoop
  - windows
title: Scoop 环境搭建
---

# Scoop 环境

<!--toc:start-->

- [Scoop 环境](#scoop-环境)
  - [什么是Scoop？](#什么是scoop)
  - [如何安装 Scoop？](#如何安装-scoop)
    - [GitHub加速](#github加速)
    - [安装 Scoop](#安装-scoop)
    - [国内镜像](#国内镜像)
    - [安装基础软件](#安装基础软件)
    - [安装其他软件源](#安装其他软件源)
  - [常用命令](#常用命令)
  <!--toc:end-->

## 什么是Scoop？

我们在Windows安装软件的流程，一般是找到对应网站的下载地址，下载并安装。这种方法有以下缺点：

- 无法保障软件下载的是正确的（有时甚至有病毒）
- 软件安装位置不好管理
- 有些软件需要设置系统环境变量，管理麻烦
- 软件的升级和卸载比较麻烦
- 操作烦琐

因此，目前有一种软件，用于管理软件的安装、升级和卸载，Scoop就是其中之一。其他软件还有：`Chocolaty` 和 `winget`。

## 如何安装 Scoop？

### GitHub加速

首先要确保能够访问 [GitHub](https://github.com)。参考方法：
[[Github Acc|GitHub 加速方法]]，如果觉得这个有困难，可以直接使用[国内镜像](#国内镜像)。

### 安装 Scoop

右击 `我的电脑` 或者 `此电脑`，选择属性，找到高级系统设置，找到高级选项卡，单击环境变量。

在上方的用户变量中，单击新建，新建一个名为 `SCOOP` 的变量，该变量是一个路径，这个路径将包含一切 `scoop` 的相关文件（包括安装的软件）。注意，这个目录最好不要包含中文，且不需要管理员权限。

在开始菜单中，搜索 `powershell`，找到后打开（不需要管理员权限）。

打开 `scoop` 的[官网]([Scoop](https://scoop.sh/)，找到安装命令。安装命令有两行，复制第一行到 `powershell` 中，按回车执行，再复制第二行命令到 `powershell` 中，按回车执行。在 `powershell` 中单击右键即为粘贴）。现在的命令为：

```pwsh
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

有的电脑只有一个管理员用户（Administrator），则需要使用以下命令安装 `scoop`：

```pwsh
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
iex "& {$(irm get.scoop.sh)} -RunAsAdmin"
```

### 国内镜像

[Scoop 国内镜像](https://gitee.com/scoop-installer/scoop)

### 安装基础软件

在 `powershell` 中输入：

```pwsh
scoop install git aria2
```

此命令安装了两个软件，`git` 用于安装其他的软件源，`aria2` 用于加速下载。

### 安装其他软件源

```pwsh
scoop bucket add extras
```

## 常用命令

搜索软件 `software`：

```pwsh
scoop search software
```

安装软件 `software`

```pwsh
scoop install software
```

卸载软件 `software`

```pwsh
scoop uninstall software
```

查看已经安装的软件：

```pwsh
scoop list
```

升级软件 `software`

```pwsh
scoop update software
```

清除所有软件的旧版本：

```pwsh
scoop cleanup *
```

删除所有下载的安装包：

```pwsh
scoop cache rm *
```
