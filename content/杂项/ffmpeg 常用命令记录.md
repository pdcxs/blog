这一内容会随着我的日常使用逐渐增多。

## 改变音频播放速率

直接改变可以用以下命令：

```bash
ffmpeg -i input.mp3 -filter:a "atempo=0.9" output.mp3
```

如果需要保持音调不变，则需要：

```bash
ffmpeg -i input.mp3 -filter:a "rubberband=tempo=0.9" output.mp3
```

注意，此命令可能会需要 `librubberband`，可以先试一下，不行的话再看看如何安装。

## 压缩视频

最常用的方法是改变 `CRF`，`CRF` 越低，质量越高，文件越大。一般而言，18-28 是可接受范围。

```bash
ffmpeg -i input.mp4 -vcodec libx264 -crf 23 output.mp4
```

