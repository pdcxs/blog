---
id: Github Acc
aliases: []
tags:
  - github
title: Github 加速方法
---

## Windows下的github加速方法

### 翻墙

这是最可靠的方式。推荐可以找几个有需求的朋友一起买一个 [JustMySocks](https://justmysocks4.com/)，注意，这个网站可能会改变，可以有必应搜索查找 Just My Socks。
需要安装一个 v2rayn，把买好的服务的订阅地址填入即可。
通过以下命令可以设置 git 使用代理：

```bash
git config --global http.proxy http://127.0.0.1:10809
git config --global https.proxy https://127.0.0.1:10809
```

最后的这个10809的数字是 V2RayN 软件的默认数字，最新版本应该默认变为了10808，如果有修改，需要填入修改后的数字。可以在 V2RayN 软件界面在左下角看到。

## 其他方案

这些方案都不是很稳定。

可以使用 `xboxdownload`，在 [gitee](https://gitee.com/wanghui999999/XboxDownload) 上下载即可。

没有翻墙工具，可以考虑采用 SwitchHosts：
SwitchHosts可以采用scoop安装，也可以在Github上下载[Releases · oldj/SwitchHosts (github.com)](https://github.com/oldj/SwitchHosts/releases)
在SwitchHosts上新建Host，远程，地址为：https://raw.hellogithub.com/hosts

还可以使用[Watt Toolkit - 瓦特工具箱(Steam++官网) (steampp.net)](https://steampp.net/)，其中包含了Github加速。

最后，可以使用[dotnetcore/FastGithub](https://github.com/dotnetcore/FastGithub)，通过运行程序或者运行服务的方式进行github加速。但是此方法会导致onedrive无法登录。

## Linux下的github加速方法（含WSL）

#wsl

对于`WSL`，目前来说直接打开 `WSL` 的图形化设置界面，直接把网络模式改为镜像可以解决所有问题。

直接的`linux`系统中，实验了很多方法，最靠谱的是直接使用[dotnetcore/FastGithub](https://github.com/dotnetcore/FastGithub)，可以先使用<https://raw.hellogithub.com/hosts>，临时改变hosts来登录github，然后下载FastGithub，启动服务，即可。
