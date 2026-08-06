---
tags:
  - github
---

## 生成 ssh key

分别运行以下命令（注意邮箱名称换成自己的）

```
ssh-keygen -t ed25519 -C "pdcxs@outlook.com"

ssh-add ~/.ssh/id_ed25519
```

## 添加 ssh key

在 Github 主页上，点击自己的头像，选择 `Settings`，选择 `SSH and GPG keys`，点击 `New SSH key` 按钮，自己起一个名字，Key type 选择 Authentication Key，将 `~/.ssh/id_ed25519.pub` 中的所有内容拷贝到 Key 当中，点击 `Add SSH key` 按钮即可。

## 需要注意

如果本地仓库的 `remote` 地址是 `https` 开头的，则无法使用 `ssh`，需要设置为以下形式：

```
git remote set-url origin git@github.com:用户名/仓库名.git
```
