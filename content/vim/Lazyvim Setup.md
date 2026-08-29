---
id: Lazyvim Setup
aliases: []
tags:
  - vim
  - neovim
  - lazyvim
  - xdg
title: LazyVim 配置
---

## 配置路径

如果需要自定义配置路径，则先设置好 [[XDG Setup|xdg 目录设置| xdg 目录]]，再安装 `neovim`，将 `nvim` 配置文件夹放置在 `XDG_CONFIG_HOME` 路径中。

## 没有说明的快捷键

#### snacks explorer

在`snacks explorer`中，按`?`是帮助。
`H`: 显示隐藏文件
`I`: 显示忽略文件
`i`: 进行搜索

#### lazygit

在已经安装了`lazygit`的前提下，不用再自行配置`lazygit`插件，只需要 `<leader>gg`即可打开`lazygit`

#### blink-cmp

一种较为舒适的补全键盘方案：(`lua/plugins/blink.lua`)

```lua
return {
  "Saghen/blink.cmp",
  opts = {
    keymap = {
      preset = "super-tab",
	  ["<CR>"] = { "accept", "fallback" },
    },
  },
}
```

快捷键：`<c-n> <c-p>` 用于选择下一个和上一个，回车用于确定选择，`tab` 用于snippets的跳转。

#### 禁止 xmake.lua 自动格式化

在`autocmd.lua`中添加：

```lua
vim.api.nvim_create_autocmd("FileType", {
  pattern = "lua",
  callback = function()
    -- 获取当前缓冲区文件名（不含路径）
    local filename = vim.fn.fnamemodify(vim.api.nvim_buf_get_name(0), ":t")
    -- 仅当文件名为 xmake.lua 时禁用格式化
    if filename == "xmake.lua" then
      vim.b.autoformat = false
    end
  end,
})

```

#### 多光标

注意，使用以下配置，在按 `Esc` 退出多光标模式后，需要再执行 `gl` 以保证 `<Leader>` 正常工作。

```lua
return {
  "brenton-leighton/multiple-cursors.nvim",
  version = "*", -- Use the latest tagged version
  opts = {}, -- This causes the plugin setup function to be called
  keys = {
    { "<C-A-j>", "<Cmd>MultipleCursorsAddDown<CR>", mode = { "n", "x" }, desc = "Add cursor and move down" },
    { "<C-A-k>", "<Cmd>MultipleCursorsAddUp<CR>", mode = { "n", "x" }, desc = "Add cursor and move up" },

    { "<C-A-Up>", "<Cmd>MultipleCursorsAddUp<CR>", mode = { "n", "i", "x" }, desc = "Add cursor and move up" },
    { "<C-A-Down>", "<Cmd>MultipleCursorsAddDown<CR>", mode = { "n", "i", "x" }, desc = "Add cursor and move down" },

    { "<C-LeftMouse>", "<Cmd>MultipleCursorsMouseAddDelete<CR>", mode = { "n", "i" }, desc = "Add or remove cursor" },

    {
      "<Leader>m",
      "<Cmd>MultipleCursorsAddVisualArea<CR>",
      mode = { "x" },
      desc = "Add cursors to the lines of the visual area",
    },

    { "<Leader>a", "<Cmd>MultipleCursorsAddMatches<CR>", mode = { "n", "x" }, desc = "Add cursors to cword" },
    {
      "<Leader>A",
      "<Cmd>MultipleCursorsAddMatchesV<CR>",
      mode = { "n", "x" },
      desc = "Add cursors to cword in previous area",
    },

    {
      "gb",
      "<Cmd>MultipleCursorsAddJumpNextMatch<CR>",
      mode = { "n", "x" },
      desc = "Add cursor and jump to next cword",
    },
    { "gB", "<Cmd>MultipleCursorsJumpNextMatch<CR>", mode = { "n", "x" }, desc = "Jump to next cword" },

    { "gl", "<Cmd>MultipleCursorsLock<CR>", mode = { "n", "x" }, desc = "Lock virtual cursors" },
  },
}
```

#### xmake集成

```lua
return {
  "Mythos-404/xmake.nvim",
  version = "^3",
  lazy = true,
  event = "BufReadPost",
  config = true,
}
```

#### LazyExtras

输入 `:LazyExtras`进行插件管理，包括各种语言，可以安装：

- coding.blink
- coding.mini-surround
- coding.yanky
- editor.dial
- editor.snacks_explorer
- editor.snacks_picker

#### 停止 Markdown 拼写检查

```lua
-- autocmds
vim.api.nvim_del_augroup_by_name("lazyvim_wrap_spell")
```

#### Markdown-preview

安装LazyExtras安装无法浏览器预览，需要按照官网指示：

```lua
return {
  "iamcco/markdown-preview.nvim",
  cmd = { "MarkdownPreviewToggle", "MarkdownPreview", "MarkdownPreviewStop" },
  build = "cd app && yarn install",
  init = function()
    vim.g.mkdp_filetypes = { "markdown" }
  end,
  ft = { "markdown" },
}
```

在安装了 `nodejs` 和 `yarn` 后，重新安装 `markdown-preview`，才可以。

如何重新安装？`<leader>l` 进入 Lazy 界面，选择 `markdown-preview`，按 gb

### 一种浅色主题

```lua
-- lua/plugins/rose-pine.lua
return {
  "rose-pine/neovim",
  name = "rose-pine",
  config = function()
    vim.cmd("colorscheme rose-pine-dawn")
  end,
}
```

### Neovide 集成

```lua
-- options.lua
vim.o.guifont = "CaskaydiaCove Nerd Font:h18"
vim.g.neovide_cursor_vfx_mode = "railgun"
vim.g.neovide_scale_factor = 1.0

-- keymaps.lua
if vim.g.neovide then
  vim.keymap.set({ "n", "v" }, "<C-=>", ":lua vim.g.neovide_scale_factor = vim.g.neovide_scale_factor + 0.1<CR>")
  vim.keymap.set({ "n", "v" }, "<C-->", ":lua vim.g.neovide_scale_factor = vim.g.neovide_scale_factor - 0.1<CR>")
  vim.keymap.set({ "n", "v" }, "<C-0>", ":lua vim.g.neovide_scale_factor = 1<CR>")
end
```

### 默认的常用快捷键

- `<leader>,` 搜索buffer
- explorer中按 `<c-v>` vsplit open
- `<c-/>` or `<leader>ft` toggle terminal: root directory
- `<leader>fT` terminal: current work directory
- `<leader>w`窗口相关操作
- `<leader>b`buffer相关操作
- 窗口与buffer相关操作：`o`：关闭其他，`d`：关闭当前
- 下一个 buffer 和上一个 buffer：`]b` `[b
- 退出软件：`<leader>qq`
- `<C-hjkl>` 移动到其他窗口
- `<leader>cs` 打开大纲视图
- `<leader>p` 打开粘贴历史
- `<leader>e` or `<leader>E` 打开文件浏览器，根目录与当前目录

### Clangd

[[Windows clangd + mingw|Windows 平台配置 clangd + mingw]]

## Root 目录

![[lazyvim 中root_dir 设置]]
