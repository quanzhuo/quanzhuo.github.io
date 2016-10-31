---
layout: post
title: "怎么做 patch"
categories: VCS
tags: patch
---

* content
{:toc}

有多种方法可以生成 patch。分享一下使用 diff，patch 或者 使用 git 制作 patch 的方法。
diff 以及 git diff 命令的输出比较怪异，但是它们又是差异比较的关键所在。patch 文件的内容
一般都是统一 diff 格式。所以正确理解 diff 的格式输出十分重要。




传统的 diff，patch 命令
=======================
diff 和 patch 命令是开发者最早开始使用的用于在开发中进行差异比较，应用 patch 的命令。
据说在 linux 内核开发社区采用 Bitkeeper 作为版本控制系统之前，Linus 就一直坚持使用 diff 
和 patch 命令来和管理代码，review 其他开发者的提交。因此首先来看看 diff 和 patch 命令。

文件 test1 的内容：

```bash
    $ cat test1
    hello 'diff'
    diff and
    patch
    are
    two
    very useful
    command for text compare
```
    
文件 test2 的内容：

```bash
    $ cat test2
    hello diff
    diff and
    patch
    is
    two
    very useful
    commands for text compare
    There is also a command called colordiff
```
    
对比两个文件：

```bash
    $ diff -u test*
    --- test1       2016-09-10 12:35:27.627768500 +0800
    +++ test2       2016-09-10 12:43:27.373208300 +0800
    @@ -1,7 +1,8 @@
    -hello 'diff'
    +hello diff
     diff and
     patch
    -are
    +is
     two
     very useful
    -command for text compare
    +commands for text compare
    +There is also a command called colordiff
```
    
如何理解 diff 命令的输出
------------------------
1. 以上输出的前两行指明了参加对比的两个文件：test1，test2。第一行行前的减号表明以下输出中以
`-`开头的行代表文件 test1 中独有的内容；第二行行前的加号表明以下输出中以 `+` 开头的是文件
test2 中独有的内容。前两行的后面是时间。

2. 第三行 `@@ -1,7 +1,8 @@` 的意思是说，现在开始一个**差异小节**。由于内容过少，上面的输出
中只有这一个差异小节，如果有多个差异小节，那么 diff 的输出中就会有多个以 `@@ ***** @@`开始
的段落。差异小节中间的 `-1,7` 表明该差异小节代表文件 test1 从第 1 行开始的 7 行，也就是
1——7 行；差异小节中的 `+1,8` 是说该差异小节同时代表文件 test2 从第 1 行开始的 8 行，也
就是 1——8 行。

3. 第四行开始，一直到下一个 `@@` 之间的内容是差异小节的内容。在差异小节中以 `-` 开始的行是 
test1 中独有的，以 `+` 开始的行是 test2 独有的，以空格开始的行是两个文件中都存在的。

生成patch
---------

    diff -u test1 test2 > test.diff
 
应用 patch 还原文件
-----------------
    
	patch test1 < test.diff
    
在 git 中使用 patch
======================
git 中提供了方便的生成 patch 的命令，该命令生成的 patch 包含所有的提交信息，可以直接通过
git am 命令应用 patch，自动创建提交，十分方便。

使用 git format-patch 生成 patch
---------------------------------
format-patch 是 git 中专门生成 patch 的命令。使用 format-patch 命令会为每次提交创建一个
patch。每个 patch 都是一个单独的文件。patch 中含有本次提交中包含的作者、提交者、comment
等所有的信息。协作者使用你提供的 由format-patch 生成的 patch 文件，可以直接使用 `git am`
命令应用 patch 并自动提交。`git format-patch` 命令在你的本地仓库中执行，**不需要同步到
gerrit 上。**

例如，我在 base 仓库中为当前分支的最新 3 次提交创建三个 patch

    $ git format-patch -3 HEAD
    0001-C5F-3171-C5-Google-security-patch-for-bsp.patch
    0002-C5F-3171-C5-Google-security-patch-for-bsp.patch
    0003-C5F-3171-C5-Google-security-patch-for-bsp.patch

假如当前分支上的最新的三次提交是你的协作者没有的。你就可以通过上面的命令生成三个 patch 发给
他。对方使用 git am 命令就可以应用。

如果你想生成 patch 的那次提交位于代码树的中间，只需在命令行中指定 `commit ID` 即可：

    git format-patch -n commit
    
其中的 n 表明为相对于本次提交的前 n 次生成patch。

使用 git am 应用 patch
-----------------------
得到别人的 patch 之后，可以直接使用 git am 应用 patch：

    git am  0001-C5F-3171-C5-Google-security-patch-for-bsp.patch
    
这样，就自动的生成了一次提交。

应用 patch 也可以通过 git apply，但是 git apply 并不会自动为你创建提交。它只是为你修改了
文件。仍然需要你自己添加修改，创建提交。推荐使用 git am 来应用patch。

如何修订提交信息
--------------
有时候可能匆匆的提交了代码，但是提交信息写错了，或者是作者配置错误，导致推送不到远程仓库。
这时候可以使用 git commit 的 --amend 参数进行修订。修改之后提交历史中只有正确的那次提交。

修改提交信息：
        
    git commit --amend
    
修改作者，邮箱等：

    git commit --amend --reset-author


更多信息
-------
    
    git help format-patch
    git help am
    git help diff    