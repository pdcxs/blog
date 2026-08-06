---
tags:
  - linux
---

## 问题

`Omarchy` 操作系统（可能任何 `Hyprland` 桌面环境都有这个问题），即使在 `hyprland` 设置中，默认开始了输入时禁用触控板，但是，没有啥效果。

## 解决方案

在 `/usr/share/libinput/` 目录下，新建文件 `local-overrides.quirks`，并编辑其内容为：

```
[Keyd Virtual Keyboard]
MatchUdevType=keyboard
MatchBus=usb
AttrKeyboardIntegration=internal
```

重启电脑即可。
