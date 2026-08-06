---
tags:
  - linux
---

## 问题

笔记本电脑直接使用触控板开关在 `hyprland` 桌面系统上不起作用。可以采用 `hyprctl` 命令打开或者关闭触控板，但是却无法查看当前触控板的开启状态。

## 脚本

通过 `hyprctl devices` 找到触控板插件的名称，如，我的名称是：`elan1203:00-04f3:307a-touchpad`

编写以下脚本(将设备名称换成自己的)：

```bash
#!/bin/bash

# 状态文件路径
STATE_FILE="/tmp/touchpad_state_${USER}"

# 函数：获取当前状态（从文件读取）
get_touchpad_status() {
    if [[ -f "$STATE_FILE" ]]; then
        cat "$STATE_FILE"
    else
        # 如果状态文件不存在，默认为开启状态
        echo "true"
    fi
}

# 函数：保存状态到文件
save_touchpad_status() {
    echo "$1" > "$STATE_FILE"
}

# 函数：切换触控板状态
toggle_touchpad() {
    local current_status=$(get_touchpad_status)

    if [[ "$current_status" == "true" ]]; then
        # 禁用触控板
        hyprctl keyword "device[elan1203:00-04f3:307a-touchpad]:enabled" "false"
        save_touchpad_status "false"
        echo "🟢 触控板已禁用"
    else
        # 启用触控板
        hyprctl keyword "device[elan1203:00-04f3:307a-touchpad]:enabled" "true"
        save_touchpad_status "true"
        echo "🟢 触控板已启用"
    fi
}

# 函数：强制设置特定状态
set_touchpad() {
    local target_status="$1"

    hyprctl keyword "device[elan1203:00-04f3:307a-touchpad]" "$target_status"
    save_touchpad_status "$target_status"

    if [[ "$target_status" == "true" ]]; then
        echo "🟢 触控板已启用"
    else
        echo "🟢 触控板已禁用"
    fi
}

# 函数：显示当前状态
show_status() {
    local status=$(get_touchpad_status)
    if [[ "$status" == "true" ]]; then
        echo "触控板状态: 🔵 启用"
    else
        echo "触控板状态: ⚫ 禁用"
    fi
}

# 主程序
case "${1:-toggle}" in
    "toggle")
        toggle_touchpad
        ;;
    "on"|"enable")
        set_touchpad "true"
        ;;
    "off"|"disable")
        set_touchpad "false"
        ;;
    "status")
        show_status
        ;;
    "help"|"--help"|-"h")
        echo "触控板控制脚本 (状态文件方案)"
        echo "用法: $0 [命令]"
        echo ""
        echo "命令:"
        echo "  toggle     - 切换触控板状态 (默认)"
        echo "  on|enable  - 启用触控板"
        echo "  off|disable - 禁用触控板"
        echo "  status     - 显示触控板状态"
        echo "  help       - 显示此帮助信息"
        echo ""
        echo "状态文件: $STATE_FILE"
        ;;
    *)
        echo "错误: 未知命令 '$1'"
        echo "使用 '$0 help' 查看可用命令"
        exit 1
        ;;
esac
```

将其添加运行权限后放入 `PATH` 变量中即可。

## 快捷键

在 `Omarchy` 系统中，可以通过编辑 `Setup > Keybindings` 来实现切换快捷键：

```
bindd = SUPER ALT, T, Toggle Touchpad, exec, $terminal -e touchpad-toggle
```
