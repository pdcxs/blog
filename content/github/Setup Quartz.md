---
id: Setup Quartz
aliases: []
tags:
  - github
  - quartz
draft: false
title: Quarzd 配置教程记录
---

本项目是依赖 [Quartz](https://github.com/jackyzha0/quartz)项目实现的，整个实现过程记录如下。

## 项目初始化

按照[官网](https://quartz.jzhao.xyz/)的说法，执行以下命令

```bash
git clone https://github.com/jackyzha0/quartz.git
cd quartz
npm i
npx quartz create
```

然后一切按照默认选择即可。

## 配置内容

首先，需要删除 `doc` 目录。这是帮助文件，删除可以减少体积。

其次，需要将 `quartz.config.tx`中的以下选项分别改为对应值：

| 选项名称  |          更改内容           |
| :-------: | :-------------------------: |
| pageTitle |          网站名称           |
|  locale   |           "zh-CN"           |
|  baseUrl  | 要发部的网址，不要加 `http` |

注意，如果在 `github` 上发部， `baseUrl` 需要加项目名称。例如，我的用户名为 `pdcxs`，我的仓库名为 `quartz`，则 `baseUrl` 应当为： `pdcxs.github.io/quartz`。

## 更改字体

直接在配置文件中更改字体有两个限制：

1. 只能使用 `Google Fonts`，能使用的字体有限。
2. 同样是因为 `Google Fonts`，国内访问可能有问题。

因此，需要直接更改 `quartz/styles/custom.scss` 的内容。

例如，本项目使用了[霞鹜文楷字体](https://github.com/Alluofuyo/LxgwWenKaiNerdFont)。查看[国内CDN](https://cdn.jsdelivr.net/npm/lxgw-wenkai-webfont@1.6.0/)的内容，直接将其[内容](https://cdn.jsdelivr.net/npm/lxgw-wenkai-webfont@1.6.0/lxgwwenkai-regular.css)拷贝到 `custom.scss`中即可。

例如，我的部分文件内容如下：

```scss
@use "./base.scss";

// put your custom CSS here!

/* LXGW WenKai [4] */
@font-face {
  font-family: "LXGW WenKai";
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url("https://cdn.jsdelivr.net/npm/lxgw-wenkai-webfont@1.6.0/files/lxgwwenkai-regular-subset-4.woff2")
    format("woff2");
  unicode-range:
    U+1f1e9-1f1f5, U+1f1f7-1f1ff, U+1f21a, U+1f232, U+1f234-1f237, U+1f250-1f251, U+1f300,
    U+1f302-1f308, U+1f30a-1f311, U+1f315, U+1f319-1f320, U+1f324, U+1f327, U+1f32a, U+1f32c-1f32d,
    U+1f330-1f357, U+1f359-1f37e;
}
/* LXGW WenKai [5] */
@font-face {
  font-family: "LXGW WenKai";
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url("https://cdn.jsdelivr.net/npm/lxgw-wenkai-webfont@1.6.0/files/lxgwwenkai-regular-subset-5.woff2")
    format("woff2");
  unicode-range:
    U+fee3, U+fef3, U+ff03-ff04, U+ff07, U+ff0a, U+ff17-ff19, U+ff1c-ff1d, U+ff20-ff3a, U+ff3c,
    U+ff3e-ff5b, U+ff5d, U+ff61-ff65, U+ff67-ff6a, U+ff6c, U+ff6f-ff78, U+ff7a-ff7d, U+ff80-ff84,
    U+ff86, U+ff89-ff8e, U+ff92, U+ff97-ff9b, U+ff9d-ff9f, U+ffe0-ffe4, U+ffe6, U+ffe9, U+ffeb,
    U+ffed, U+fffc, U+1f004, U+1f170-1f171, U+1f192-1f195, U+1f198-1f19a, U+1f1e6-1f1e8;

  // 后面内容为在线 css 的内容的拷贝
}
```

最后，在 `custom.scss` 文件中，加入以下内容即可：

```scss
:root {
  --bodyFont: "LXGW WenKai" !important;
  --headerFont: "LXGW WenKai" !important;
  --codeFont: "Cascadia Code" !important;
}
```

最后，执行以下命令：

```bash
npx quartz build --serve
```

即可在浏览器中打开<http://localhost:8080/>查看效果了。

## 上传 `github`

参考[官网](https://quartz.jzhao.xyz/setting-up-your-GitHub-repository)，先新建一个名为 `quartz` 的仓库，不要包含 `README`、`.gitignore` 和 `LICENSE`。

按照[[Git Set Remote Address|Git如何设置远程地址]]中的方法，设置好远程地址。

需要再执行以下命令来删除 `upstream` 地址：

```bash
git remote rm upstream
```

最后，执行以下命令：

```bash
npx quartz sync --no-pull
```

## 部署

按照[官网](https://quartz.jzhao.xyz/hosting)指示。
