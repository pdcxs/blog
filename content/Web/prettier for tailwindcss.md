---
id: prettier for tailwindcss
aliases: []
tags:
  - web
title: 如何配置prettier格式化tailwindcss
---

## 安装依赖

```bash
npm install -D prettier prettier-plugin-tailwindcss
```

## 设置配置文件

在工程目录下，添加文件 `prettier.config.js`，内容为：

```js
/** @type {import('prettier').Config} */
const config = {
  trailingComma: "none",
  plugins: ["prettier-plugin-tailwindcss"],
}

export default config
```

## Lazyvim 插件

在 `LazyExtras` 中，安装 `prettier` 插件。

在 nvim 的配置文件中的 `/lua/config/options.lua` 中，添加：

```lua
vim.g.lazyvim_prettier_needs_config = false
```
