---
layout: post
title: "AOSP 构建系统"
categories: Android
tags: build
---

* content
{:toc}

AOSP 构建系统是一个十分庞大和复杂的编译系统，且仍然一直在发展当中。从 Android 6.0 开始，
Google 一直致力于加快编译速度，为使增量开发更加便捷，对 AOSP 的编译系统进行了较大的更改，
编译工具由 make 变更为 ninja。本文将深入介绍 AOSP 的构建系统。



## GNU make 构建系统

## ninja 构建系统

### kati

### soong

### blueprint

## AOSP 的构建过程

### 导入 envsetup.sh

编译时，首先需要导入文件 `build/envsetup.sh`，导入该文件之后，当前环境中就会有一些额外
有用的命令（实际是 shell 函数）可以执行。

+ lunch：
+ tapas
+ croot：切换到 aosp 工程的根目录
+ m：在项目任何子文件夹中执行该命令都相当于在根目录中执行 make
+ mm：构建当前文件夹下的所有模块，不包含依赖
+ mmm：
+ mma：同 mm，包含依赖
+ mmma：同 mmm，包含依赖
+ cgrep：grep on c/c++ 文件
+ ggrep：gradle 文件
+ jgrep：java
+ resgrep：res/\*.xml文件
+ mangrep：AndroidManifest.xml
+ mgrep：Makefile
+ sepgrep：sepolicy 文件
+ sgrep：所有的 source 
+ godir：
+ core:
+ cproj
+ grepprebuilt
+ pid
+ systemstack


导入 `build/envsetup.sh` 之后，输入 `hmm` 即可看到导入的所有的函数。

在文件 `build/envsetup.sh` 中同时会导入位于 `device, vendor, product` 目录下面的
`vendorsetup.sh` 文件。OED/ODM 厂商可以在这些文件中调用函数 `add_lunch_combo` 来往
lunch 菜单中添加自己的产品配置。

例如在文件 `device/moto/shamu/vendorsetup.sh` 中有下面的语句：

    add_lunch_combo aosp_shamu-userdebug

aosp_shamu-userdebug 就出现在了 lunch 的菜单项目中。

### lunch

下一步就是使用 lunch 菜单选择相应的构建目标（target）。选择构建目标之后，会导出一些环境
变量：

* `TARGET_PRODUCT=$product`： 构建目标
* `TARGET_BUILD_VARIANT=$variant`: 构建变种（eng，userdebug，user)
* `TARGET_BUILD_TYPE=release`

     Only release type is available. Use choosecombo if you want to select type.

* `ANDROID_BUILD_TOP=$(gettop)`：The build  root directory.
* `export ANDROID_TOOLCHAIN=...`

    The toolchain directory for the prebuilt cross-compiler matching the target
    architecture

* `PATH=...`：Among other stuff, the prebuilt toolchain is added to PATH.
* `ANDROID_PRODUCT_OUT=...`：Absolute path to the target product out directory
* `ANDROID_HOST_OUT=...` – Absolute path to the host out directory

### 开始构建

上面我们已经设置好了构建环境，接着就可以开始构建 Android 系统的各个镜像了，直接在命令行
输入 `make` 即可开始构建安卓镜像。一次典型的构建一般可以生成一下镜像：

* boot.img – Native system boot image.
* ramdisk.img – Ramdisk rootfs.
* recovery.img – Recovery image.
* ramdisk-recovery.img – Ramdisk rootfs for Recovery.
* system.img – System data (/system directory)
* userdata.img – User data (/data directory)

上面的镜像一起组成了 Android 系统。但是要注意，boot.img 并不是 AOSP 的 一部分。必须另
外进行构建。

