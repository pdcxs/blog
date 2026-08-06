---
tags:
  - lazyvim
---

## root_spec 设置

在 `options.lua`文件中，有以下选项：

```lua
-- LazyVim root dir detection
-- Each entry can be:
-- * the name of a detector function like `lsp` or `cwd`
-- * a pattern or array of patterns like `.git` or `lua`.
-- * a function with signature `function(buf) -> string|string[]`
vim.g.root_spec = { "lsp", { ".git", "lua" }, "cwd" }
```

可以加入我们自己的目录检测设置，例如，对于xmake项目，可以检测是否存在 `xmake.lua` 文件：

```lua
vim.g.root_spec = {
  "lsp",
  { "xmake.lua", ".git", "lua" },
  "cwd",
}
```

## lsp 相关

有时，即使设置了 `root_spec` 选项，也有可能会出现目录检测出问题的情况。例如，我碰到了在 `nvim` 设置目录中打开一个文件，但是根目录检测到了用户目录的情况。

这种情况一般是 `lsp` 函数调用时，检测出了问题。例如，我碰到的这种情况，其实可以参考 [lua lsp 的规则](https://github.com/neovim/nvim-lspconfig/blob/9b2509f17c284486497358ccea1019cc17c28af6/lua/lspconfig/configs/lua_ls.lua#L4-L10)，然后发现，我的用户目录不清楚为什么出现了 `.luarc.jsonc`，删除后就正常了。为了避免再出现这个问题，可以把 `root_spec` 选项只保留 `cwd`，手动控制是最直观的。

此外，`jdtls.nvim` 需要特别注意，参见[[lazyvim 折腾 Java 环境踩坑记录]]。
