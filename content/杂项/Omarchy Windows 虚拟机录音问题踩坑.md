---
tags:
  - linux
---

## 问题

鉴于腾讯会议现在依然无法支持 `Wayland` 屏幕共享，因此我准备在 Windows 虚拟机中安装腾讯会议，如果需要共享屏幕，就用 Windows 里面的虚拟机。

但是，我发现 Windows 虚拟机中居然无法接收到声音！查看日志输出，有以下内容：

```
[aac @ 0x7f3d621532c0] Profile not supported!
[aac @ 0x7f3d621532c0] Qavg: nan
[20:11:25:348] [21559:00005471] [ERROR][com.freerdp.dsp.ffmpeg] - [ffmpeg_open_context]: Error avcodec_open2 Invalid argument [-22]
```

## 解决思路

问题的关键应该是 `ffmpeg` 的 `aac` 解码问题，其 `Profile` 有若干不同选项，而 `rdp` 链接刚好采用了一种目前不支持的选项。

因此，指定一下 `microphone` 的编码就行了。

找到：`~/.local/share/omarchy/bin/omarchy-windows-vm`，定位到 `xfreerdp3` 的位置（只有一处，搜索就可以了），将其内容从原来的：

```bash
xfreerdp3 /u:"$WIN_USER" /p:"$WIN_PASS" /v:127.0.0.1:3389 -grab-keyboard /sound /microphone /cert:ignore /title:"Windows VM - Omarchy" /dynamic-resolution /gfx:AVC444 /floatbar:sticky:off,default:visible,show:fullscreen $RDP_SCALE
```

改为：

```bash
xfreerdp3 /u:"$WIN_USER" /p:"$WIN_PASS" /v:127.0.0.1:3389 -grab-keyboard /sound:sys:alsa /microphone:sys:alsa,format:1 /cert:ignore /title:"Windows VM - Omarchy" /dynamic-resolution /gfx:AVC444 /floatbar:sticky:off,default:visible,show:fullscreen $RDP_SCALE
```

就可以了！
