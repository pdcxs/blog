---
id: Setup LazyVim And NeoVide in WSL
aliases: []
tags:
  - vim
  - lazyvim
  - neovim
  - wsl
title: WSL 中配置 LazyVim 与 NeoVide
---

## 安装依赖

根据 [LazyVim官网](https://www.lazyvim.org)，需要安装以下软件：

- `build-essentials`
- `fzf`
- `curl`
- `git`
- `ripgrep`
- `fd` (Ubuntu中叫 `fd-find`)
- 图形化终端(用 `NeoVide` 可以忽略)
- `lazygit`
- 任意 `Nerd Font`

## Neovim

执行以下命令：

```bash
sudo add-apt-repository ppa:neovim-ppa/unstable
sudo apt-get update
sudo apt-get install neovim
```

## Neovide

在 `Windows` 下安装即可：

#windows #scoop

```pwsh
scoop install neovide
```

## 配置

### Neovim

在 `wsl` 中安装 `lazyvim`

### Neovide

可以先[[WSL fonts|WSL 中共享 Windows 字体]]。

增加以下命令到 `.bashrc` 中:

```bash
alias nvi=neovide.exe --wsl
```

这样就可以在 `wsl` 中用 `nvi` 命令直接启动 `neovide` 了。其中，`neovide` 安装在 `windows` 中，但是会自动加载 `wsl` 中 `neovim` 的程序和配置。哪怕 `windows` 中也安装了 `neovim`，也不会互相干扰。
