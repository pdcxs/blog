---
id: UV for airsim
title: 使用 uv 构建 AirSim 项目
tags:
  - airsim
  - python
  - uv
  - Academic
---

[AirSim](https://microsoft.github.io/AirSim/apis/) 的 API 有点太老了，如果使用新的 Python 项目管理工具 uv 会设置起来有点困难，这里记录一下。

## Python 版本选择

建议选择3.10，使用以下命令新建工程：

```bash
cd /path/to/your/project
uv init --python 3.10
```

## 添加依赖

AirSim 不仅依赖于官方文档中的 `msgpack-rpc-python`，还依赖于 `numpy` 和 `setuptools`，因此需要执行以下命令：

```bash
uv add msgpack-rpc-python numpy setuptools
```

最后再执行

```bash
uv add airsim
```

## Mathematica 集成

如果需要在 Mathematica 中调用，参考：[Python Session in Mathematica|Mathematica集成]，执行以下命令：

```bash
uv add pyzmq
```
