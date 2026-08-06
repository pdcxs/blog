---
tags:
  - linux
---

## 问题

我使用的是 `omarchy` 操作系统，是基于 Arch Linux 和 `Hyprland` 的。我发现在安装了 AUR 版本的wemeet-bin 后，无法共享屏幕。

## 解决方案

安装 flatpak 版本的旧 wemeet 即可。

### 安装 Flatpak

```bash
sudo pacman -S flatpak
```

### 配置国内源（重要）

默认的 Flatpak 源，wemeet 缺少很多旧版本。必需换成国内源才可以：

```bash
flatpak remote-modify flathub --url=https://mirrors.ustc.edu.cn/flathub
```

### 查看旧版本

执行以下操作，查看有哪些旧版本可选：

```bash
flatpak remote-info --log flathub com.tencent.wemeet
```

我执行后可以看到以下版本：

```
    Commit: a43efff41927d4d630bf01d0b06b915496c863658c2e8d730989017cfbd5ff68
   Subject: Merge pull request #53 from fxzjshm/flatpak-pipewire (f21064edef4b)
      Date: 2025-08-01 14:03:30 +0000
```

### 安装腾讯会议

```bash
sudo flatpak install com.tencent.wemeet
```

### 回退到指定版本

```bash
sudo flatpak update --commit=a43efff41927d4d630bf01d0b06b915496c863658c2e8d730989017cfbd5ff68 com.tencent.wemeet
```

注意，这里的commit就是之前查到的内容

### 锁定版本

执行以下命令，锁定腾讯会议，避免系统自动升级：

```bash
sudo flatpak mask com.tencent.wemeet
```

现在，应该可以正常共享屏幕了。期待后续能有更好的解决方案。
