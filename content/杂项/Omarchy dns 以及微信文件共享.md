---
tags:
  - linux
---

## DNS 设置

一般 Linux 设置 DNS 需要编辑 `/etc/resolv.conf`，不过在 Omarchy 系统中，需要按 `Super+Alt+Space` 打开系统菜单，在其中找到：`Setup` -> `DNS` 中进行设置。

## 微信文件共享

我直接使用 AUR 安装了[微信](https://aur.archlinux.org/packages/wechat?O=140)，但是，在微信中，如果想要发送或者接收文件，无法直接访问系统文件系统，因为其在一个沙盒中运行。

经过探索，发现其可以访问的目录位于： `~/.local/share/WeChat_Data/` 。

所以，想要发送文件，将文件拷贝至此目录即可，想要找到接收的文件，也需要在此文件夹中寻找。
