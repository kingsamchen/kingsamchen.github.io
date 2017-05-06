---
title: 用 FFMpeg 生成视频缩略图
categories: PROGRAMMING
date: 2017-05-06 22:21:18
tags: [ffmpeg, thumbnails]
---
除了可以使用 [ffmpeg 压制视频外](https://kingsamchen.github.io/2017/04/12/use-ffmpeg-to-reencode-videos/)，还能利用 ffmpeg 生成某个视频的缩略图。

利用命令行：

```
ffmpeg.exe -skip_frame nokey -i "some_video.mp4" -vsync 0 -vframes 9 -c:v mjpeg "output_dir\thumb_%d.jpg"
```

就可以在 `output_dir` 这个目录下为视频文件 *some_video.mp4* 从头开始生成最多不超过9张关键帧缩略图，文件名分别是 `thumb_1.jpg` ~ `thumb_9.jpg`。

这个默认策略适合长度较短的视频（比如半分钟或一分钟内），对于长视频，很可能跑完9张关键帧缩略图，正片都还没开始...

所以，这个时候的生成策略应该考虑能够尽可能的均匀分布于视频内容。

首先利用这个 [post](https://kingsamchen.github.io/2017/04/12/use-ffmpeg-to-reencode-videos/) 里提到的做法，用 `ffprobe.exe` 获得视频的长度，记为 **_T_**（单位，秒）。

将 **_T_** 均分为 10（9+1）个片段，取 $ t_i = \frac{T}{10} * i, i = 1, 2, \cdots 9 $，这么做的原因是避免取到开头和结尾。

最后针对每个 $ t_i $，运行 ffmpeg

```
ffmpeg.exe -ss t_i -skip_frame nokey -i "some_video.mp4" -vsync 0 -vframes 1 -c:v mjpeg "output_dir\thumb_i.jpg"
```

唯一麻烦的地方在于输出文件名序列需要我们自己指定。