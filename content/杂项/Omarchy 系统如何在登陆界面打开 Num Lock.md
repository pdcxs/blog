---
tags:
  - linux
---

## 安装软件

安装软件 `mkinitcpio-numlock`：

```bash
yay mkinitcpio-numlock
```

## 配置

编辑 `/etc/mkinitcpio.conf.d/omarchy_hooks.conf` 文件，在 `encrypt` 前面添加 `numlock`，例如，我的文件内容为(不要直接复制文件，不同的系统版本可能会有不同，直接添加 `numlock` 即可)：

```
HOOKS=(base udev plymouth keyboard autodetect microcode modconf kms keymap consolefont block numlock encrypt filesystems fsck btrfs-overlayfs)
```

## 加载配置

运行以下命令：

```
sudo mkinitcpio -P
```

如果需要确认，输入 `y` 进行确认即可。
