---
id: Keyboard Setup on Windows
aliases: []
tags:
  - vim
  - windows
title: Windows 系统的键盘配置
---

# Windows 系统的键盘配置

在 Linux 系统下，我们可以使用 keyd 进行键盘配置，参见[[Arch Linux Keyboard Setup|Arch Linux 系统下的键盘配置]]。但是在 Windows 系统下如何实现？

## 配置方法

首先，需要安装 [AutoHotKey](https://www.autohotkey.com/) 软件。

然后，新建一个后缀名为 `.ahk` 的文件，用记事本打开，编辑其内容如下：

```ahk
#Requires AutoHotkey v2.0
#SingleInstance Force

; ==============================================================================
; 1. CapsLock 逻辑:
;    - 按住 CapsLock + H/J/K/L 映射为方向键
;    - 单独按下并释放 CapsLock 触发 Esc
; ==============================================================================
*CapsLock:: {
    Send "{Blind}{vkE8}"
    KeyWait "CapsLock"
    if (A_PriorKey == "CapsLock") {
        Send "{Blind}{Esc}"
    }
}

#HotIf GetKeyState("CapsLock", "P")
*h::Send "{Blind}{Left}"
*j::Send "{Blind}{Down}"
*k::Send "{Blind}{Up}"
*l::Send "{Blind}{Right}"

; 常用 Vim 扩展导航（按需取消注释）:
*u::Send "{Blind}{Home}"
*i::Send "{Blind}{End}"
*d::Send "{Blind}{Delete}"
#HotIf

; ==============================================================================
; 2. 原 Esc 键映射为 CapsLock 大小写切换
; ==============================================================================
*Esc::SetCapsLockState(GetKeyState("CapsLock", "T") ? 0 : 1)
```

之后保存，双击打开这个文件就可以了。

## 如何开机启动

将这个文件保存到任意位置，新建一个快捷方式，在 Windows 的资源管理器的路径中，输入：
`shell:common startup`，并将快捷方式放到打开的目录中即可。
