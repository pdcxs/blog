---
tag:
  - github
  - lazygit
  - xdg
---

`Lazygit` 是我一直在使用的管理 git 仓库的工具。其默认的界面语言为 auto，大多数时候会自动检测为中文。但是很多功能需要进行搜索，此时英文更加方便，因此需要将语言转换转换为中文。

## 配置文件路径

按照[官网](https://github.com/jesseduffield/lazygit/blob/master/docs/Config.md)说法，其配置文件位于

- Linux: `~/.config/lazygit/config.yml`
- MacOS: `~/Library/Application\ Support/lazygit/config.yml`
- Windows: `%LOCALAPPDATA%\lazygit\config.yml` (default location, but it will also be found in `%APPDATA%\lazygit\config.yml`

但是，如果设置了 [[xdg 目录设置| xdg 目录]]，则其配置文件会位于：
`%XDG_CONFIG_HOME%\lazygit\config.yml` 中

## 更改配置文件

在配置文件中设置以下内容即可：

```yml
gui:
  language: "en"
```
