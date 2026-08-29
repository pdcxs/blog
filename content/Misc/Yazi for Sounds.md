---
id: Yazi for Sounds
aliases: []
tags:
  - windows
title: 如何把 yazi 当成音效管理软件
---

## 安装 yazi

按照[官网](https://yazi-rs.github.io/docs/installation)的说法，分别安装：

```bash
scoop install ffmpeg
scoop install 7zip
scoop install jq
scoop install poppler
scoop install fd
scoop install ripgrep
scoop install fzf
scoop install zoxide
scoop install imagemagick
scoop install yazi
```

设置 `YAZI_FILE_ONE` 环境变量的值为：`C:\scoop\apps\git\current\usr\bin\file.exe` （具体内容根据 `git` 安装路径需要更改）

## 安装插件

我编写了一个显示音频时长和波形的插件：<https://github.com/pdcxs/wave-preview.yazi>

运行：

```bash
ya pkg add "pdcxs/wave-preview"
```

在 `%APPDATA%/Roaming/yazi/config/yazi.toml` 中添加

```toml
[plugin]
prepend_previewers = [
    { mime = "audio/*", run = "wave-preview" },
]
prepend_preloaders = [
    { mime = "audio/*", run = "wave-preview" },
]
```

## 使用

先切换到指定的文件夹，然后按 `s` 进行搜索，搜索到结果后，按 `,s` 按大小排序，这样音效会排在前边。

如果安装了 `mpv`，那么可以在 `yazi.toml` 中添加：

```toml
[opener]
play_audio = [
    { run = 'mpv --force-window --keep-open=no %s', block = false, desc = "Play audio with mpv" },
]

[open]
rules = [
    { mime = "audio/*", use = "play_audio" },
]
```

即可。
