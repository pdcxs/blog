---
tags:
  - linux
---

## 问题

需要开机启动 `V2rayN`，并且自动将其放到 `Scratchpad` 工作区。

## 解决方案

### 开机启动

执行以下命令：

```bash
cp /usr/share/applications/v2rayN-bin.desktop ~/.config/autostart/
```

### 自动移动到 `scratchpad` 工作区

按 `Super + Alt + Space` 快捷键，进入系统选单，分别进入：`Setup -> Config -> Hyprland`，在配置文件中添加：

```
windowrule = workspace special:scratchpad, match:class v2rayN
```
