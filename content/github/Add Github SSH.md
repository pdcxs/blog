---
id: Add Github SSH
aliases: []
tags:
  - github
title: 如何为 Github 添加 SSH
---

## 如何添加 ssh-key

首先检查一下自己是否有 `~/.ssh/id_rsa.pub` 文件，如果没有，执行以下命令：

```bash
ssh-keygen -t rsa -C "pdcxs@outlook.com"
```

此处的邮箱需要换成自己的 GitHub 邮箱。

然后把 `~/.ssh/id_rsa.pub` 中的所有内容复制，在 GitHub 的账户设置界面中，选择 `SSH and GPG keys`，并添加一个新的 `SSH Key`，名称随便，类型为 `Authentication Key`，内容为刚刚复制的内容。

理论上讲，现在应当可以在克隆的 `ssh` 项目中进行同步了（注意，必需是 ssh 地址，即以 `git@` 开头的地址）。

## 还是没有权限？

如果还是拒绝访问，可以尝试在 `~/.ssh` 目录下，新建一个 `config` 文件，其内容为：

```
Host github.com
  Hostname ssh.github.com
  Port 443
  User git
```

有可能可以解决。其思路是利用 `https` 端口来执行操作，因为有时系统会拦截 `ssh` 端口。

## 添加 ssh key

在 Github 主页上，点击自己的头像，选择 `Settings`，选择 `SSH and GPG keys`，点击 `New SSH key` 按钮，自己起一个名字，Key type 选择 Authentication Key，将 `~/.ssh/id_ed25519.pub` 中的所有内容拷贝到 Key 当中，点击 `Add SSH key` 按钮即可。
