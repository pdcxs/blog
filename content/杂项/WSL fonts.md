---
id: WSL Fonts
aliases: []
tags:
  - wsl
title: WSL 共享 Windows 字体
---

创建 `/etc/fonts/local.conf` 文件，在其中添加：

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
    <dir>/mnt/c/Windows/Fonts</dir>
</fontconfig>
```

执行 `fc-cache -fv`

执行 `fc-list` 确认。
