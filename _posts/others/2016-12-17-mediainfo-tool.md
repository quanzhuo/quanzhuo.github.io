---
layout: post
title:  "使用 mediainfo 获取音视频文件头信息"
categories: tools
tags:  mediainfo
---

* content
{:toc}

最近一直使用喜马拉雅FM听《易中天品三国》，易中天讲的真是精彩。今天洗脚的时候想用电脑听，就把下
载到手机上的文件复制到计算机上了。可是发现下载下来的文件名全都是 40 Byte 的 SHA1 字串。无法
找到确定的某一集。



就想写个小工具读取一下音频文件的头信息，将文件重命名一下。经过一番搜索发现了两个专门用于读取音
视频文件头信息的 C++ 库：

+ [taglib](https://taglib.github.io)
+ [mediainfo](https://mediaarea.net/ZH-CN/MediaInfo)

这两个库都提供有 c 语言的接口。本来准备写个简单的 C 程序的。后来发现 mediainfo 这个工具提供
有一个 GUI 版本（mediainfo-gui）和一个 CLI 版本（mediainfo）的工具，还有开发
库（libmediainfo-dev）和文档（libmediainfo-doc）。在 Ubuntu 的官方仓库中
可以直接安装：

    sudo apt install mediainfo-gui mediainfo 
    sudo apt install libmediainfo-dev libmediainfo-doc

GUI 版本的工具可以直接打开某一个音频文件或者一个包含音频文件的文件夹，就可以显示文件的头信息：
专辑，作者，标题，采样率等等各种你需要的信息。

命令行的工具接受一些选项，可以获得你需要的信息。

    mediainfo [-Options...] FileName1 [Filename2...]

例如：

```bash
$ mediainfo 0af013591ebd7d3fc144250528a20f2c  
General  
Complete name                            : 0af013591ebd7d3fc144250528a20f2c  
Format                                   : MPEG-4  
Format profile                           : Apple audio with iTunes info  
Codec ID                                 : M4A  (isom/iso2)  
File size                                : 6.22 MiB  
Duration                                 : 35mn 52s  
Overall bit rate mode                    : Constant  
Overall bit rate                         : 24.3 Kbps  
Album                                    : 百家讲坛_《易中天品三国》  
Track name                               : 0af013591ebd7d3fc144250528a20f2c  
Performer                                : 易中天  
Recorded date                            : 2006  
Encoded date                             : UTC 1904-01-01 00:00:00  
Tagged date                              : UTC 1904-01-01 00:00:00  
Writing application                      : Lavf56.18.101  
```

既然有了命令行工具，那么通过 bash 脚本可以快速的解决问题，就在终端中敲了下面的一段命令：

```bash
for fname in `ls`
do
    title=`mediainfo $fname | grep "Track name" | cut -d":" -f2 | awk '{$1=$1};1'`
    echo "rename $fname to $title"
    mv $fname $title
done
```

解决了问题。

喜马拉雅FM打的推广口号：“让做家务变得津津有味“，我发现这个真的是一点儿也不夸张，一边听广播，我
这么懒的人，洗衣服也不觉得枯燥了...