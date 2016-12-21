---
layout: post
title:  "Windows 系统上的 alias"
categories: Sys-admin
tags:  
---

* content
{:toc}

我们知道，Linux 系统上的 bash 内建命令 `alias` 可以用来创建命令别名。命令别名给工作
带来了极大的方便。那么 Windows 系统上是否也有这种便利呢？ Windows 提供了一个内建的
`doskey` 命令可以达到和 Linux 中的 `alias` 一样的效果。



这是 doskey 的语法：

```
C:\Users\Administrator>doskey /?
编辑命令行，重新调用 Windows 命令，并创建宏。

DOSKEY [/REINSTALL] [/LISTSIZE=size] [/MACROS[:ALL | :exename]]
  [/HISTORY] [/INSERT | /OVERSTRIKE] [/EXENAME=exename] [/MACROFILE=filename]
  [macroname=[text]]
```

如果你安装了 Gow （GNU on Windows，是一个工具包，安装之后可以提供 100 多个 GNU 
Linux 上面常用的命令，是 cygwin 的一个轻量级的替代品），想让 ls 命令像在 linux 上面
一样，输出时自动以色彩区分文件类型（其实在 linux 上，默认色彩输出也是通过 alias 实现
的），你可以在 cmd 中执行以下指令：

    doskey ls=ls --color $*

这样你在该 cmd 窗口中就可以得到默认的色彩输出了。但是如果重新打开一个 cmd 窗口，刚才的
设置就失效了。可以在你的 PATH 环境变量的位置之一创建一个 bat 文件,然后通过写入注册表
来让每次打开一个 cmd 窗口时都自动执行该 bat 文件。这跟 linux 上面 non-login shell 的
初始化文件 `.bashrc` 的原理是一模一样的。

例如，假设在 `c:\Users\Administrator\bat` 文件夹（该路径须要加入 PATH 环境变量）下
面有一个批处理文件 `alias.bat`，在里面加入了一些 alias：

```
@echo off
doskey ls=ls --color=always $*
doskey ll=ls -lh --color=always $*
doskey grep=grep --color=always $*
```

然后打开注册表，定位到 `HKEY_CURRENT_USER\Software\Microsoft\Command Processor\`，
添加一个字符串，命名为 Autorun，将它的值设置为
 `c:\Users\Administrator\bat\alias.bat`，这样以后每次打开 cmd 窗口就可以立即使用
 这些命令别名了。

 参考：[superuser](http://superuser.com/questions/55351/how-to-change-the-default-color-preference-for-ls-on-windows)