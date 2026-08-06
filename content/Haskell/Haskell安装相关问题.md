---
tags:
  - haskell
  - ghcup
  - scoop
  - xdg
---

## XDG设置

如果想要使用 `XDG` 目录设置，最好安装前运行以下脚本：

![[xdg 目录设置#设置脚本 (make_xdg.ps1)]]

## GHCUP

首先是 `ghcup` 的安装：（如果要使用 `XDG` 目录，最好设置好环境变量后再安装）

```pwsh
scoop install ghcup
```

这样会把 `ghcup` 安装到名为 `SCOOP` 的环境变量所指定的目录中，如果这个环境变量没有设置，会安装到：`~/scoop` 目录中。

### 环境变量

在使用 `ghcup` 正式安装软件之前，先运行 `ghcup --help`，在帮助信息的最后，会有如下内容：

```
ENV variables:
  * GHCUP_INSTALL_BASE_PREFIX: the base of ghcup (default: $HOME)
  * GHCUP_USE_XDG_DIRS: set to anything to use XDG style directories
```

[官网说明](https://www.haskell.org/ghcup/guide/#xdg-support)如下

To enable XDG style directories, set the environment variable `GHCUP_USE_XDG_DIRS` to anything.

Then you can control the locations via XDG environment variables as such:

- `XDG_DATA_HOME`: GHCs will be unpacked in `ghcup/ghc` subdir (default: `~/.local/share`)
- `XDG_CACHE_HOME`: logs and download files will be stored in `ghcup` subdir (default: `~/.cache`)
- `XDG_BIN_HOME`: binaries end up here (default: `~/.local/bin`)
- `XDG_CONFIG_HOME`: the config file is stored in `ghcup` subdir as `config.yaml` (default: `~/.config`)

我在 Windows 系统下尝试了设置 `XDG` 相关变量，但是没有效果。后来发现了原因：如果要使用 `XDG`，则不能再设置 `GHCUP_INSTALL_BASE_PREFIX`。设置 `GHCUP_INSTALL_BASE_PREFIX` 会直接设置安装目录。我将此环境变量设置为了 `D:\software\haskell`，它会在这个目录下新建一个 `ghcup` 文件夹并保存内容到里面。

在设置了 `GHCUP_INSTALL_BASE_PREFIX` 变量后，`config.yaml` 会移动至该下的 `ghcup/config.yaml` 处。

可以运行以下命令以彩中科大的源：

```pwsh
ghcup config set url-source "https://mirrors.ustc.edu.cn/ghcup/ghcup-metadata/ghcup-latest.yaml"
```

## Stack

运行 `ghcup list`，安装推荐的 `stack` 版本。

设置环境变量 `STACK_ROOT`，用以保存 `stack` 的相关文件。这个和 `ghcup` 的行为不同，不会在 ROOT 下面新建目录，所以我设置为了 `D:\software\haskell\stack`。注意，需要把 `GHCUP_INSTALL_BASE_PREFIX` 下的 `ghcup\bin` 添加到 `PATH` 变量中才能运行 `stack`。

同理，如果不设置 `STACK_ROOT`，直接设置 `STACK_XDG` 为 `true`，则可以使用 `xdg` 目录。

此外，根据 [Stack 官网](https://docs.haskellstack.org/en/v2.11.1/stack_root/)说明，如果存在 `LOCALAPPDATA` 环境变量，则相关工具（如 `msys2`、下载的 `ghc` 版本等等，这些才是大头）会安装到 `%LOCALAPPDATA%/Programs/stack/` 目录中。如果没有这个变量，则会安装到 `%STACK_ROOT%/programs%` 下。由于 `LOCALAPPDATA` 这个环境变量很重要，不能直接删除，因此可以在 `config.yaml` 中指定 `local-programs-path`。

在 `stack_root` 下，新建 `config.yaml`，添加内容(作者信息根据自己修改，local-programs-path 也需要根据实际情况更改)：

```yaml
templates:
  params:
    author-name: pdcxs
    author-email: pdcxs@outlook.com
    copyright: Apache
    github-username: pdcxs

system-ghc: true

setup-info-locations: ["https://mirrors.tuna.tsinghua.edu.cn/stackage/stack-setup.yaml"]
urls:
  latest-snapshot: https://mirrors.tuna.tsinghua.edu.cn/stackage/snapshots.json

snapshot-location-base: https://mirrors.tuna.tsinghua.edu.cn/stackage/stackage-snapshots/

local-programs-path: "D:/software/haskell/stack/programs"
```

## 其他

运行 `ghcup tui` 安装其他内容
