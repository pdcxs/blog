---
id: Windows clangd + mingw
aliases: []
tags:
  - windows
  - c++
title: Windows 平台配置 clangd + mingw
---

如果电脑只安装了 `mingw` 而没有 `msbuild-tool`，那么 `clangd` 可能无法识别头文件。

需要在 `$env:appdata/clangd/config.toml` 文件中，添加以下内容：

```toml
CompileFlags:
  Add:
    - --target=x86_64-w64-windows-gnu
```

参考：[--query-driver not having any effect · Issue #537 · clangd/clangd](https://github.com/clangd/clangd/issues/537#issuecomment-1479544442)
