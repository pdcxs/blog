---
id: WattToolkit
aliases: []
tags:
  - github
  - xmake
title: Watt Toolkit 加速方案
---

## 什么是 Watt Toolkit？

[Watt Toolkit](https://steampp.net/)，中文名为瓦特工具箱，之前称为 steam++，是一个用来加速 steam 访问的工具。但是，由于其带有 [[Github Acc|GitHub 加速方法]] 加速功能，且是免费的，所以是一个不错的加速工具。

不过，由于 `Watt Toolkit` 默认只加速网页访问，不加速 `git` 等工具，因此需要一些配置。

## 如何安装？

建议直接在微软商店进行安装即可。

## 如何配置系统代理？

### Watt Toolkit 设置

打开 `Watt Toolkit` 后，在左侧的“网络加速”选项卡中，在平台加速选项卡中，勾选“公共CDN”与“Github"两个选项，其他内容根据需要自行选择。

在右上角的“加速设置”中，加速模式选择“PAC”模式，打开“代理设置”，点击“重置”按钮以恢复到默认选项，并记住代理监听端口的端口号（默认应当为26561。后续内容都以此端口号为例）

点击“一键加速”按钮开始加速。

### 系统设置

按 `Win+I` 快捷键，打开 `Windows 设置`。选择 `网络和Internet` -> `代理`，点击`使用代理服务器`右侧的编辑按钮。

1. 开启`使用代理服务器`选项
2. `代理IP地址`为`127.0.0.1`
3. 端口为 `Watt Toolkit` 中的监听端口，默认为26561
4. 开启`请勿将代理服务器用于本地(Intranet)地址`选项
5. 在`请勿对以下列条目开头的地址使用代理服务器`中，填写：

```
localhost;127.*;10.*;172.16.*;172.17.*;172.18.*;172.19.*;172.20.*;172.21.*;172.22.*;172.23.*;172.24.*;172.25.*;172.26.*;172.27.*;172.28.*;172.29.*;172.30.*;172.31.*;192.168.*;login.partner.microsoftonline.cn;login.microsoftonline.com
```

点击`保存`按钮。

## `git` 加速

输入以下命令以设置 `git` 使用 Watt Toolkit 代理：

```bash
git config set --global http.proxy "http://127.0.0.1:26561"
git config set --global https.proxy "http://127.0.0.1:26561"
git config set --global http.sslVeryfi false
```

在确定一切设置都正确且开启了 Watt Toolkit 加速的前提下，`git` 应当可以正常访问 `github` 了。

## xmake 加速

如果 `xmake` 需要下载一些第三方包，那么也需要经常访问 `github`，可以采用以下命令：

```bash
xmake g --proxy="http://127.0.0.1:26561"
xmake g --insecure-ssl=y
```

## scoop 加速

执行以下命令：

```bash
scoop config proxy "127.0.0.1:26561"
```
