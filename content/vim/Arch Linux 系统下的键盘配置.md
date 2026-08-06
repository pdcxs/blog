---
tags:
  - vim
  - linux
---

## 问题描述

我们在使用 vim 时，经常需要使用 `esc` 键，但是这个按键离得手指舒适区域非常远。因此，一种常见的做法是：更换 `esc` 按键和 `caps lock` 按键。

使用 `keyd` 是一种比较有效的解决方案，此外，还有一个好处是，可以进一步设置 `caps lock` 键的功能：短按是 `esc` 键，长按则是切换键盘布局，在新的布局下，可以将 h, j, k, l 分别映射为左、下、上和右，这样在一些非 `vim` 环境下，也可以在长按 `caps lock` 键的情况下，使用 `vim` 的操作逻辑了。

## 安装

执行以下命令：

```bash
sudo pacman -S keyd
```

## 配置

编辑 `/etc/keyd/default.conf` 文件，我的文件内容如下：

```
[ids]
*

[main]
capslock = overload(caps_layer, esc)
esc = capslock

# asdf 长按为左修饰键，短按为字母
a = overloadt2(control, a, 200)
s = overloadt2(shift, s, 200)
d = overloadt2(alt, d, 200)
f = overloadt2(meta, f, 200)

# jkl; 长按为右修饰键，短按为字母
j = overloadt2(meta, j, 200)
k = overloadt2(alt, k, 200)
l = overloadt2(shift, l, 200)
semicolon = overloadt2(control, semicolon, 200)

[caps_layer]
u = home
i = end
y = pageup
o = pagedown
h = left
j = down
k = up
l = right
```

## 启用

执行以下命令：

```bash
sudo systemctl enable --now keyd.service
```
