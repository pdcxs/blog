---
id: My Own Typst Chinese Template
aliases: []
tags:
  - typst
title: 自己用的中文排版模板
---

`Office Word` 或者 `WPS` 在使用时，经常碰到非常麻烦的格式调整过程。于是我想自己写一个平时常用的中文排版的 `Typst` 模板，可以快速开始编写文件。之后可以考虑用 `typ2docx` 等工具，来将其转换成 `Word` 与他人共享。不过这一工具目前不支持 CJK 字符，我已经向作者提交了 issue，等待其完善才能使用。

## 模板内容

我将模板分为了默认版本与SFC版本，其中，默认版本偏向于行政文件的排版，而SFC则偏向于科研相关的文件类型的排版，其内容如下（在`utils.typ` 文件中）

```typst
#import "@preview/cuti:0.4.0": show-cn-fakebold
#import "@preview/pointless-size:0.1.2": zh
#import "@preview/numbly:0.1.0": numbly
#import "@preview/itemize:0.2.0" as el
#import "@preview/i-figured:0.2.4"

#let setup-style(
  doc,
  // 标题：2号 方正小标宋简体
  heading-font-family: ("Times New Roman", "FZXiaoBiaoSong-B05S"),
  heading-font-size: zh(2),
  // 正文：3号 仿宋_GB2312
  regular-font-family: ("Times New Roman", "FangSong_GB2312"),
  regular-font-size: zh(3),
  // 正文行距：20pt
  leading: 20pt - 1em,
  // 正文段间距：1.5字符
  par-spacing: 1.5em,
  // 标题: 段前2字符，段后2字符
  heading-above: 2em,
  heading-below: 2em,
  // 一级标题: 黑体三号 序号"一、"
  heading1-font-family: ("Times New Roman", "SimHei"),
  heading1-font-size: zh(3),
  // 一级标题: 段前1.5字符，段后1.5字符
  heading1-above: 1.5em,
  heading1-below: 1.5em,
  // 二级标题: 楷体_GB2312 三号
  heading2-font-family: ("Times New Roman", "KaiTi_GB2312"),
  heading2-font-size: zh(3),
  // 二级标题: 段前1.5字符，段后1.5字符
  heading2-above: 1.5em,
  heading2-below: 1.5em,
  // 三级标题: 仿宋_GB2312 三号
  heading3-font-family: ("Times New Roman", "FangSong_GB2312"),
  heading3-font-size: zh(3),
  // 三级标题: 段前1.5字符，段后1.5字符
  heading3-above: 1.5em,
  heading3-below: 1.5em,
  // 四级标题: 仿宋_GB2312 三号
  heading4-font-family: ("Times New Roman", "FangSong_GB2312"),
  heading4-font-size: zh(3),
  // 三级标题: 段前1.5字符，段后1.5字符
  heading4-above: 1.5em,
  heading4-below: 1.5em,
  heading-numbering: numbly(
    "",
    "{2:一}、",
    "({3:一})",
    "{4}. ",
    "({5}) ",
  ),
  paper: "a4",
  margin: (
    top: 37mm,
    bottom: 35mm,
    left: 28mm,
    right: 26mm,
  ),
  page-numbering: "- 1 -",
  page-number-align: center,
  figure-numbering: "1-1",
  numbering-level: 0,
  equation-numbering: "(1.1)",
) = {
  show: show-cn-fakebold
  set page(
    paper: paper,
    margin: margin,
    numbering: page-numbering,
    number-align: page-number-align,
  )

  set text(lang: "zh", top-edge: 0.7em, bottom-edge: -0.3em)

  set par(
    first-line-indent: (amount: 2em, all: true),
    leading: leading,
    justify: true,
    spacing: par-spacing,
  )

  show heading.where(level: 1): it => {
    pagebreak(weak: true)
    align(center)[
      #set text(font: heading-font-family, size: heading-font-size)
      #block(above: heading-above, below: heading-below)[
        #it
      ]
    ]
  }
  show heading: i-figured.reset-counters.with(level: numbering-level)

  show math.equation.where(block: true): it => i-figured.show-equation(
    it,
    numbering: equation-numbering,
    level: numbering-level,
  )
  set math.equation(supplement: "公式")

  // 表格
  show figure.where(kind: table): set figure.caption(position: top)
  set figure.caption(separator: "  ")
  show figure.caption: it => text(size: regular-font-size, it)

  show figure: i-figured.show-figure.with(
    numbering: figure-numbering,
    level: numbering-level,
  )

  show math.equation.where(block: true): i-figured.show-equation.with(level: numbering-level)
  set math.equation(supplement: "公式")

  show heading.where(level: 2): pad.with(left: 2em)
  show heading.where(level: 3): pad.with(left: 2em)
  show heading.where(level: 4): pad.with(left: 2em)
  show heading.where(level: 5): pad.with(left: 2em)

  show heading.where(level: 2): it => {
    set text(
      font: heading1-font-family,
      size: heading1-font-size,
      weight: "bold",
    )

    block(
      above: heading1-above,
      below: heading1-below,
    )[
      #it
    ]
  }

  show heading.where(level: 3): it => {
    set text(
      font: heading2-font-family,
      size: heading2-font-size,
      weight: "bold",
    )

    block(
      above: heading2-above,
      below: heading2-below,
    )[
      #it
    ]
  }

  show heading.where(level: 4): it => {
    set text(
      font: heading3-font-family,
      size: heading3-font-size,
      weight: "bold",
    )

    block(
      above: heading3-above,
      below: heading3-below,
    )[
      #it
    ]
  }

  show heading.where(level: 5): it => {
    set text(
      font: heading4-font-family,
      size: heading4-font-size,
      weight: "bold",
    )

    block(
      above: heading4-above,
      below: heading4-below,
    )[
      #it
    ]
  }

  set heading(numbering: heading-numbering)

  set text(
    font: regular-font-family,
    size: regular-font-size,
  )

  show: el.default-enum-list

  doc
}

#let SFC = setup-style.with(
  heading-font-family: ("Times New Roman", "KaiTi"),
  heading-font-size: zh(3),
  regular-font-family: ("Times New Roman", "KaiTi"),
  regular-font-size: zh(-4),
  heading1-font-family: ("Times New Roman", "KaiTi"),
  heading1-font-size: zh(4),
  par-spacing: 1.5em,
  heading2-font-family: ("Times New Roman", "KaiTi"),
  heading2-font-size: zh(-4),
  heading3-font-family: ("Times New Roman", "KaiTi"),
  heading3-font-size: zh(-4),
  heading4-font-family: ("Times New Roman", "KaiTi"),
  heading4-font-size: zh(-4),
  heading-numbering: numbly(
    "",
    "{2}. ",
    "{2}.{3} ",
    "{2}.{3}.{4} ",
    "{2}.{3}.{4}.{5} ",
  ),
  margin: (y: 2.54cm, x: 3.2cm),
  page-numbering: none,
  numbering-level: 0,
)
```

## 相关字体下载

如果使用默认风格，则需要下载以下字体：

- 方正小标宋简体
- 仿宋\_GB2312
- 楷体\_GB2312

可以从以下网址下载：

<https://office.sdufe.edu.cn/info/1062/3546.htm>

## 使用方法：

以下是两种使用示例：

### 直接使用默认设置：

```typst
#import "./utils.typ": setup-style
#show: setup-style
= -> 标题
== -> 一级标题
=== -> 二级标题
==== -> 三级标题
===== -> 四级标题
```

### 进行微调

```typst
#import "./utils.typ": SFC
#import "@preview/pointless-size:0.1.2": zh
#show: SFC.with(heading1-font-size: zh(3))

正文内容
```
