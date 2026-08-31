---
id: Python Session in Mathematica
title: 如何在 Mathematica 中调用 Python
tags:
  - Mathematica
  - python
  - Academic
---

我们以 `uv` 工程为例，说明如何创建自己的工程并在 `Mathematica` 中调用。

## 创建工程

新建一个空目录，在目录中执行：

```bash
uv init
```

## 安装 `pyzmq`

`Mathematica` 调用 `Python`，需要 `Python` 安装 `pyzmq` 包。在 `uv` 工程中，只需要执行：

```bash
uv add pyzmq
```

即可。

## 定位 `Python` 位置

在 `uv` 工程目录下，执行以下命令：

```bash
uv run python -c "import sys; print(sys.executable)"
```

即可得到当前 `python` 可执行程序的路径。

## 注册 `Python` 环境

在 `Mathematica` 中，执行以下命令进行测试：

```Mathematica
ClearAll["`*"];
RegisterExternalEvaluator["Python",
  "Your/Path/To/Python"];
session = StartExternalSession["Python"];
ExternalEvaluate[session, "print(2+2)"]
DeleteObject[session];
```

注意，路径换成之前得到的 `Python` 执行程序的路径，如果是 `Windows` 系统，记得反斜杠需要转义，如 `C:\\Python.exe`

至于更加具体的交互内容，参见：[Wolfram Mathematica Python 帮助](https://reference.wolfram.com/language/ref/externalevaluationsystem/Python.html)
