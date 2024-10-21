---
title: "ffmpeg常用用法"
date: 2024-10-21T21:53:38.751694
categories: ['Tools']
author: "Ronan"
---
# 不改变原视频码率裁剪
### 废话少说，直接举栗🌰
1.从 `1时15分25秒` 开始裁剪到视频结束： 
```shell
ffmpeg -i input.mp4 -ss 01:15:25 -c copy output.mp4
``` 

2.从 `视频开始到15分5秒` ：
```shell
ffmpeg -i input.mp4 -to 00:15:05 -c copy output.mp4
```  

3.从 `15分25秒` 到  `1时15分25秒` ：
```shell
ffmpeg -i input.mp4 -ss 15:25 -to 01:15:25 -c copy output.mp4
``` 
  
`-i` 指定输入视频路径，`input.mp4`是要裁剪的视频文件的路径， `-ss` 裁剪视频起始时间，`-to` 裁剪视频结束时间，`-c copy` 在裁剪时不改变视频编码，从而保持原始视频码率， `output.mp4` 为裁剪后的视频文件。

# 不改变原视频码率合并
```shell
ffmpeg -i "concat:input1.mp4|input2.mp4" -c copy output.mp4
``` 
其中，`input1.mp4`和`input2.mp4`是要合并的两个视频文件的路径。`-c copy`参数告诉FFmpeg在合并时不改变视频编码，从而保持原始视频码率。最终合并后的视频文件名为`output.mp4`。

> [!WARNING]
请注意，此命令假定两个输入视频具有相同的分辨率和帧率。如果它们不同，则可能需要使用不同的参数或进行转换。


