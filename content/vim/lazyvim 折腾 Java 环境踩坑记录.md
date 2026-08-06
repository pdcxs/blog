---
tags:
  - lazyvim
  - java
---

## 碰到的问题

直接在 `LazyExtras` 选择安装 `lang.java` 后，有以下问题：

1. 直接打开非工程中的 `Java` 文件，会有一个 `***.java is a non-project file.` 的警告。
2. 无法直接运行简单的 `Java` 文件。

## 解决思路

对于第一点，可以用 `maven` 或者 `gradle` 来新建一个工程，然后再用 `nvim` 打开这个工程。

但是，`maven` 创建工程太麻烦，`gradle` 创建的工程目录太复杂。我最终选择使用 `IntelliJ IDEA` 新建一个工程。再用 `nvim` 打开。

这里有一点需要注意：**程序的包名和项目名称需要不一样**，这个问题，`IDEA`里面没有问题，但是 `nvim` 里面会报错。

对于无法直接运行简单 `Java` 文件的问题，我添加了如下的配置：

```lua
-- autocmds.lua
vim.api.nvim_create_autocmd("FileType", {
  pattern = "java",
  callback = function()
    vim.keymap.set("n", "<leader>cj", function()
      local filename = vim.fn.expand("%:t") -- 获取带扩展名的文件名
      local file_dir = vim.fn.expand("%:p:h")
      vim.cmd("w")
      local cmd = string.format("java %s", filename)
      Snacks.terminal(cmd, {
        cwd = file_dir,
        auto_close = false,
        interactive = true,
      })
    end, { desc = "Run Java File", buffer = true })
    vim.keymap.set("n", "<leader>cJ", function()
      local filename = vim.fn.expand("%:t") -- 获取带扩展名的文件名
      local classname = vim.fn.expand("%:t:r") -- 获取类名（无扩展名）
      local file_dir = vim.fn.expand("%:p:h")
      vim.cmd("w")
      local cmd = string.format("javac %s && java %s", filename, classname)
      Snacks.terminal(cmd, {
        cwd = file_dir,
        auto_close = false,
        interactive = true,
      })
    end, { desc = "Compile & Run Java File", buffer = true })
  end,
})
```

这样，`<leader>cj` 会运行命令：`java CurrentFile.java`，`<leader>cJ` 会先将当前文件编译为 `.class` 文件再运行。

## 再次碰到问题

然而，我发现，当 `Java` 工程所在的路径中，如果存在空格，则有的系统会导致无法识别当前工程是一个工程目录（很神秘，我有两台电脑，同样的 `nvim` 配置与 `java` 环境，但是一台电脑正常，一台电脑又出现了 `non-project file` 的警告）。

问题的关键是，我想把这个工程目录放到 `Onedrive for Business` 目录中，而这个目录，名字是 `Onedrive - UserName`，不能自己改！垃圾微软，网上也有很多人在吐槽这个事情，太影响开发了。

我先进行了以下尝试：用管理员权限打开 `cmd.exe`，执行以下命令：

```bash
mklink /J new_path old_path
```

比如，我运行了以下命令：

```
mklink /J C:/Users/pdcxs/creation/java/java-cpp-material 'C:/Onedrive - pdcxs/文档/Work/课程/Java程序设计/java-cpp-material'
```

但是，当我进入 `C:/Users/pdcxs/creation/java/java-cpp-material` 目录中时，`jdtls.nvim` 依然会识别为原来的目录。经过一番挣扎后，写了如下配置，才一切正常：

```lua
-- plugins/jdtls.lua
return {
  "mfussenegger/nvim-jdtls",
  opts = function(_, opts)
    -- 强制 root_dir 为当前工作目录（符号链接路径）
    opts.root_dir = function(fname)
      return vim.fn.getcwd() -- 直接返回 Neovim 的当前工作目录
    end

    opts.settings = vim.tbl_deep_extend("force", opts.settings or {}, {
      java = {
        project = {
          referencedLibraries = {
            "lib/*.jar",
          },
        },
      },
    })
    return opts
  end,
}
```

## 后记

算了，没啥后记，配置过程中一直在吐槽微软的神奇脑回路，真是一点都不考虑开发人员的感受呀！这里就不继续骂了，累了。
