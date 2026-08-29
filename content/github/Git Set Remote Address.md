---
id: Git Set Remote Address
aliases: []
tags: []
tag:
  - github
title: Git 如何设置远程地址
---

现在的 github 不太推荐使用 ssh 了，而是推荐直接使用 personal token 来进入接入。而使用 personal token 则需要更改 remote 地址。

在 lazygit 中，可以按 3 并按 `]` 来切换到远程界面，并按 `e` 进行编辑，先输入远程名字，按回车后，可以编辑远程地址。

但是有个问题，使用 `token` 后，远程地址变得非常复杂，但是 lazygit 中的远程地址编辑不允许粘贴。

目前看来唯一的办法是直接使用 git 来进行设置：

1. 查看 git remote 地址：

```bash
git remote -v
```

2. 设置 git remote 地址：

```bash
git remote set-url origin https://[token]@github.com/[user-name]/[repo].git
```
