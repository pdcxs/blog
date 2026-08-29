---
id: MotionPro on Linux
aliases: []
tags:
  - linux
title: Arch Linux 安装 MotionPro VPN
---

## 问题

学校使用的是 `MotionPro VPN`，该 VPN 只支持 Windows、MAC、Ubuntu、RedHat和Centos。

## Arch Linux 安装

直接安装：

```bash
yay motionpro-gui
```

## 运行

安装结果后，会自动安装到目录 `/opt/MotionPro/` 当中。

如果切换到该目录并运行：

```bash
./MotionPro
```

可能会提示找不到 `libvl3vpn.so`，则可以设置库目录再运行：

```bash
LD_LIBRARY_PATH=/opt/MotionPro/ /opt/MotionPro/MotionPro
```

如果运行了 `./MotionPro.sh`，可能出现以下警告：

```
./MotionPro.sh: line 9: runlevel: command not found
```

把对应行注释掉就可以了。

**一定要注意，如果开了其他的VPN，一定要关闭！**

## 连接到校园网

打开后，添加配置：

- 网络名称随便起一个名字
- host为vpn网络地址，为：`vpn1.tyut.edu.cn`
- 用户名和密码填自己的。

添加好配置后，直接连接也不行，需要先在学校的VPN网站上登陆一下，提示无法打开本地的VPN后，再运行 MotionPro 并链接，这样才可以。
