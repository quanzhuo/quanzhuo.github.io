---
layout: post
title: "Android 镜像的解包"
categories: Android
tags:
---

* content
{:toc}

以 AOSP 为例，一次典型的构建会生成以下 image （镜像文件）：`system.img`，
`ramdisk.img`，`userdata.img`，`cache.img`，这些 image 一起构成了 Android 系统。下
面将分别介绍这些 image 如何解包。



## `system.img`

system image 是安卓系统中最大的一个镜像文件，里面包含了所有的 app，二进制可执行文件，
库文件，字体，配置文件，安卓框架核心库等等。AP 部分除了 kernel 之外，基本上都包含在
system.image 中。

system.img 文件是由文件夹 `out/target/product/$PRODUCT/system` 下面的所有文件打包而
来的。要想解包 `system.img` 文件，可以先用 file 命令看一下 `system.img` 的格式：

    file system.img

输出显示：

> system.img: Linux rev 1.0 ext4 filesystem data, UUID=da594c53-9beb-f85c-85c5-cedf76546f7a,
> volume name "system" (needs journal recovery) (extents) (large files)

可见 `system.img` 是 ext4 文件系统，那么就可以直接使用 mount 指令挂载之。

    mkdir system-img
    sudo mount system.img system-img

如果使用 file 查看 `system.img` 的输出不为 ext4 文件系统，而类似于下面：

> Android sparse image, version: 1.0, Total of 393216 4096-byte output blocks
> in 1765 input chunks.

Android sparse image 和 ext4 文件系统之间可以通过 simg2img 工具相互转换：

    simg2img <sparse-img-file> <ext4-fs>
    simg2img <ext4-fs> <sparse-img-file>

转换为 ext4 格式之后再 mount 即可。

## `userdata.img`

userdata.img 是基于 `out/target/product/$PRODUCT/data` 文件夹下面的内容生成的。解包
userdata.img 与 解包 system.img 的方法一致，不再赘述。

## `ramdisk.img`

ramdisk.img 是基于 `out/target/product/$PRODUCT/root` 文件夹下面的内容制作的。root
文件夹是 linux 系统的根文件系统。按照以下步骤可以解包。

首先仍然使用 file 查看类型：

file ramdisk.img

得到如下输出：

> ramdisk.img: gzip compressed data, from Unix

可见是经过 gzip 压缩的数据，那么就先解压。因为 ramdisk.img 并不是以 gzip 格式压缩档常
见的后缀 .gz 结尾的，所以，我们需要给 gzip 传递一个 -S 参数：

    gzip -S .img -d ramdisk.img

然后检查得到的 ramdisk 文件的类型：

    file ramdisk

ramdisk 现在是一个 cpio 档：

> ramdisk: ASCII cpio archive (SVR4 with no CRC)

cpio 档使用下面的命令可以得到其内容：

    cpio -i -F ramdisk
