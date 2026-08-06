---
tags:
  - windows
  - powershell
---

在 Windows 11 下，发现如果添加选项：

```lua
config.window-background_opacity = 0.9
```

窗口并没有透明，则需要打开 Nvidia Control Panel，选择 "3D 设置" -> "管理 3D 设置"，选择程序设置选项卡，点击添加按钮，选择 Wezterm 程序，在下方的值中，找到 "OpenGL GDI 兼容性"，设置为"优先兼容性"。 重启 Wezterm 应该就正常了。

顺便分享一下我的配置：

```lua
local wezterm = require("wezterm")

local config = wezterm.config_builder()

config.color_scheme = "Tokyo Night"
config.font = wezterm.font("CaskaydiaCove Nerd Font")
config.font_size = 18.0
config.default_prog = { os.getenv("SCOOP") .. "/apps/pwsh/current/pwsh.exe" }
config.window_decorations = "INTEGRATED_BUTTONS|RESIZE"
config.window_close_confirmation = "NeverPrompt"
config.window_background_opacity = 0.92
config.win32_system_backdrop = "Auto"
-- config.tab_bar_at_bottom = true
config.use_fancy_tab_bar = false
-- config.enable_tab_bar = false
local userprofile = os.getenv("USERPROFILE")
config.default_cwd = userprofile .. "/Desktop/"

return config
```

用 `Ctrl-Shift-Alt-"` 来水平分割窗口，用 `Ctrl-Shift-Alt-%` 来垂直分割窗口，用 `Ctrl-Shift-T` 来新建标签，`Ctrl-Shift-数字键` 指明跳转到哪个标签，或者 `Ctrl-Tab` 切换标签。因此不需要显示标签栏。

`Ctrl-Alt-Shift-箭头` 用于调整分割大小。`Ctrl-Shift-箭头` 用于在不同的 Pane 间移动，输入 `exit` 后即可关闭 Pane。`Ctrl-Shift-Z` 可以切换当前 Pane 的全屏还是正常显示。`Alt-Enter` 则可以切换 Wezterm 是否全屏。
