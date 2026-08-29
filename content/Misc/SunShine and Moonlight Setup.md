---
id: SunShine and Moonlight Setup
aliases: []
tags:
  - windows
title: SunShine 和 Moonlight 串流设置
---

基本采用了 `sunshine` + `moonlight` 的设置方法。按照官方流程反而一直没有设置成功，平板上一直不能正确得到想要的分辨率。后来参考了[这一篇博客](https://blog.csdn.net/qq_40008976/article/details/142696708)才最终设置成功。

## 软件的安装

注意，如果安装了以前版本的 `sunshine`，一定要按照 `github` 项目简介中的文档说明卸载。

此外，如果安装了虚拟显卡、虚拟显示器，也需要卸载。

- `sunshine`: https://github.com/qiin2333/Sunshine-Foundation/releases
- `moonlight`: https://github.com/qiin2333/moonlight-android/releases/tag/shortcut

在电脑上安装 `sunshine`，在平板上安装 `moonlight`。安装过程全部默认选项即可。

## Sunshine 设置

先禁用虚拟显示器

设置输出输出显示器为虚拟显示器，适配器为独显。

模式选择最后一个“禁用其他显示器，只启用指定显示器”

手动添加设备的分辨率和帧数。

保存并应用。

## Moonlight 设置

视频分辨率：本地原生

自定义分辨率：输入平板的分辨率

勾选将画面拉伸至全屏

## 分辨率 remap

可以添加分辨率重映射，需要注意的是，重映射后的分辨率和帧率都需要加入到虚拟显示器的支持列表当中。
