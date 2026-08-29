---
id: Linux Github AutoUpgrade
aliases: []
tags:
  - github
  - linux
title: Arch Linux 如何添加 Github 自动更新
---

Omarchy 是一个基于 Arch Linux 的 Linux 发行版本，其核心思路与 LazyVim 差不多，都是提前对系统进行好配置，免去自己配置的麻烦。我看其默认配置相当不错，就拿来试了一下。

然而，默认配置是针对国外用户的，有很多国内不常用的软件。此外，还有一个问题：Github 时常打不开。我的思路是写一个自动下载 Github Hosts 并自动更新的脚本，来实现这一功能。

## 脚本

脚本功能是自动下载 Gitee 上的 Hosts，然后自动配置好 `/etc/hosts` 文件。

```bash
#!/bin/bash

# 检查是否有管理员权限
if [ "$EUID" -ne 0 ]; then
    echo "需要管理员权限来修改 /etc/hosts"
    # 重新以sudo权限运行脚本
    exec sudo "$0" "$@"
    exit 1
fi

# 备份原始hosts文件
cp /etc/hosts /etc/hosts.backup
echo "已创建备份: /etc/hosts.backup"

# 删除第5行及之后的所有内容
if [ -f /etc/hosts ]; then
    # 保留前4行
    head -n 4 /etc/hosts > /etc/hosts.tmp
    mv /etc/hosts.tmp /etc/hosts
    echo "已删除第5行及之后的内容"
else
    echo "错误: /etc/hosts 文件不存在"
    exit 1
fi

# 下载并追加新的hosts内容
echo "正在下载hosts内容..."
if command -v curl &> /dev/null; then
    curl -s https://gitee.com/if-the-wind/github-hosts/raw/main/hosts >> /etc/hosts
elif command -v wget &> /dev/null; then
    wget -q -O - https://gitee.com/if-the-wind/github-hosts/raw/main/hosts >> /etc/hosts
else
    echo "错误: 未找到 curl 或 wget，请安装其中一个"
    exit 1
fi

# 检查下载是否成功
if [ $? -eq 0 ]; then
    echo "成功更新 /etc/hosts"
    echo "新的hosts文件内容:"
    echo "======================"
    tail -n 10 /etc/hosts
    echo "======================"
else
    echo "错误: 下载hosts内容失败"
    # 恢复备份
    mv /etc/hosts.backup /etc/hosts
    echo "已恢复备份文件"
    exit 1
fi
```

## 将脚本文件作为命令

假设脚本名称为 `git-hosts.sh`，执行以下命令：

```bash
mv git-hosts.sh git-hosts
chmod +x git-hosts
sudo mv git-hosts /usr/local/bin/
```

这样就可以通过手动执行 `git-hosts` 来更新 hosts 了。

## 自动定时更新

新建文件： `/etc/systemd/system/update-git-hosts.service`，在其中添加以下内容：

```
[Unit]
Description=Update Github Hosts

[Service]
Type=oneshot
ExecStart=/usr/local/bin/git-hosts
User=root
Group=root
TimeoutStartSec=30

[Install]
WantedBy=multi-user.target
```

我曾尝试过添加在 `network-online.target` 之后再运行这一命令的方式，但是发现打开 `systemd-networkd-wait-online.servie` 会严重拖慢开机速度，所以还是自己写定时器了，我想这也是为啥系统默认给这一服务加了 mask 的原因吧。

新建文件：`/etc/systemd/system/update-git-hosts.timer`，并在其中添加以下内容：

```
[Unit]
Description=Update Github Hosts Per Hour
Requires=update-git-hosts.service

[Timer]
OnBootSec=2min
OnUnitActiveSec=1h
RandomizeDelaySec=300

[Install]
WantedBy=timers.target
```

最后，执行：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now update-git-hosts.timer
```

即可启动这一服务了。

当前服务会在开机后的2分钟时自动更新 Github Hosts，并在之后每1小时自动更新。（有5分钟的随机误差，以避免不同的定时器同时执行。）
