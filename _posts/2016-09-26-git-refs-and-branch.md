---
layout: post
title: "Git 引用和分支"
categories: VCS
tags: Git Branch
---

* content
{:toc}

引用
----
git 中的 HEAD 指针，分支，标签，都是引用。引用其实就是一个包含了 SHA-1 值的文件。所谓引用
引用的就是 40 个字节的 SHA-1 值。




我们可以借助类似于 `git log 1a410e` 这样的命令来浏览完整的提交历史，但为了能遍历那段历史
从而找到所有相关对象，你仍须记住 1a410e 是最后一个提交。 我们需要一个文件来保存 SHA-1 值，
并给文件起一个简单的名字，然后用这个名字指针来替代原始的 SHA-1 值。

在 Git 里，这样的文件被称为“引用（references，或缩写为 refs）”；你可以在 .git/refs
目录下找到这类含有 SHA-1 值的文件。

    $ tree -CF .git/refs/
    .git/refs/
    ├── heads/
    │   ├── master
    │   └── new
    ├── remotes/
    │   └── origin/
    │       └── HEAD
    └── tags/

分支
-------
git 分支其实只是 `.git/refs/heads` 目录下面的一个包含了 40 个字符提交ID的文件。只使用
40 个字节的提交ID，同样可以实现分支的功能。但是 40 个字节的 ID 不好记忆。因此 git 通过
`.git/refs/heads/` 目录下的引用文件来方便的实现分支功能。下面尝试通过手动在
`.git/refs/heads/` 目录下面新建一个文件来创建一个分支：

``` bash
$ git branch
* master
$ git rev-parse master
62c432f7bfecf8a7237dbf5c00903da0b28318bc
$ echo 62c432f7bfecf8a7237dbf5c00903da0b28318bc > .git/refs/heads/bran-cre-by-hand
$ git branch
  bran-cre-by-hand
* master
$ git checkout bran-cre-by-hand
Switched to branch 'bran-cre-by-hand'    
```

可见 手动创建的分支跟 `git branch <branch-name>` 命令创建的分支是一样的。当运行类似于
`git branch <branch-name>` 这样的命令时，Git 实际上会执行相同的操作，取得当前所在分支
最新提交对应的 SHA-1 值，并将其写入 `.git/refs/heads` 目录下面的一个文件中。

HEAD
----
现在的问题是，当你执行 git branch <branch-name> 时，Git 如何知道最新提交的 SHA-1 值呢？
答案是 HEAD 文件。

HEAD 文件是一个符号引用（symbolic reference），指向目前所在的分支。 所谓符号引用，意味着
它并不像普通引用那样包含一个 SHA-1 值——它是一个指向其他引用的指针。 如果查看 HEAD 文件的
内容，一般而言我们看到的类似这样：

    $ cat .git/HEAD
    ref: refs/heads/master

如果执行 git checkout test，Git 会像这样更新 HEAD 文件：

    $ cat .git/HEAD
    ref: refs/heads/test

当我们执行 git commit 时，该命令会创建一个提交对象，并用 HEAD 文件中那个引用所指向的
SHA-1 值设置其父提交字段。

远程跟踪分支
----------
如果你添加了一个远程版本库，并对其执行过推送操作，Git 会记录下最近一次推送操作时每一个分支
所对应的值，并保存在 `refs/remotes` 目录下。 如下 git 仓库中 refs/remotes/origin 目录
下面的文件即为**远程跟踪分支**，远程跟踪分支可以使用 `git branch -r` 查看。

    $ tree -C .git/refs/
    .git/refs/
    ├── heads
    │   ├── master
    │   ├── new
    │   └── test
    ├── remotes
    │   └── origin
    │       ├── HEAD
    │       ├── new
    │       └── test
    └── tags


远程跟踪分支和本地分支（位于 refs/heads 目录下的引用）之间最主要的区别在于，
**远程跟踪分支是只读的**。虽然可以 git checkout 到某个远程跟踪分支，但是 Git 并不会将
HEAD 引用指向该远程跟踪分支。因此，你永远不能通过 commit 命令来更新远程跟踪分支，他们只能
由和远程服务器交互的命令（如 `git fetch`）来自动更新。 Git 将这些引用（远程跟踪分支）作
为记录远程服务器上各分支最后已知位置状态的书签来管理。

``` bash
$ git checkout origin/test
Note: checking out 'origin/test'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 8be26f5... commit on test
```

从上面可以看出虽然检出了远程跟踪分支 'origin/test'，但是 HEAD 处于分离头指针的状态，因此不能
在此处进行开发。必须检出本地分支才可以进行开发。

分支（本地分支）和远程跟踪分支的关系
---------------------
clone 一个项目之后，git 会为远程仓库中的所有分支都在本地创建对应的远程跟踪分支，然后创建
一个初始化分支（也即本地分支，一般 fork 自远程仓库的 master 分支），并检出该分支的代码。
如果我们想在其他分支上（非 master）工作，就必须首先创建并检出该远程跟踪分支对应的本地分支。

首先我们 clone 一个仓库：

    git clone ssh://H2404689@10.195.229.38:29418/QC/platform/frameworks/av

clone 之后，git 为我们在本地创建了所有的远程跟踪分支（只读的）：

    $ git branch -r
    origin/B5F/dev/MSM8953/00310/PFAM
    origin/B5F/dev/MSM8953/00910/PFAM
    origin/B5F/dev/MSM8953/01410/PFAM
    origin/C5F/dev/MSM89xx/02910/PFAM
    origin/C5F/dev/MSM89xx/04710/PFAM
    origin/C5F/dev/MSM89xx/05610/PFAM
    origin/C5F/dev/MSM89xx/06010/PFAM
    origin/C5F/dev/MSM89xx/V2.20A/P2
    origin/C5F/dev/MSM89xx/V2.21A/CTA
    origin/C5F/dev/MSM89xx/V2.22A/P1
    origin/C5F/dev/MSM89xx/V3.38A/FTM
    origin/HEAD -> origin/master
    origin/QC/MSM8953/00310
    origin/QC/MSM8953/00910
    origin/QC/MSM8953/01410
    origin/QC/MSM89xx/02910
    origin/QC/MSM89xx/04710
    origin/QC/MSM89xx/05610
    origin/QC/MSM89xx/06010
    origin/master

然后创建了一个本地分支 master 并检出代码。

    $ git branch
    master

但是我们检查当前工作目录中却没有文件，这是因为，该仓库的 master分支上面没有提交代码。

如果你要在 C5F/dev/MSM89xx/06010/PFAM 上提交代码，就必须首先新建一个本地分支并检出代码
才能工作：

    git branch C5F/dev/MSM89xx/06010/PFAM --track origin/C5F/dev/MSM89xx/06010/PFAM
    git checkout C5F/dev/MSM89xx/06010/PFAM

上面两条命令等同于下面一条命令：

    git checkout -b C5F/dev/MSM89xx/06010/PFAM --track origin/C5F/dev/MSM89xx/06010/PFAM

你的代码是提交在本地分支上的，然后由本地分支推送到远程。

或者有时候你会发现直接执行 `git checkout C5F/dev/MSM89xx/06010/PFAM` 也能工作。这是
因为，当 git 尝试检出 `C5F/dev/MSM89xx/06010/PFAM` 分支时候，发现并没有这样一个本地
分支。此时如果远程跟踪分支有一个这样的分支，git 就执行上面第二种形式的命令。虽然很多时候
这样也能工作，但是不清不楚容易使人迷惑。

origin 和 master
----------------
远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义。同时
“master” 是当你运行 git init 时默认的起始分支名字，原因仅仅是它的广泛使用，“origin” 是
当你运行 git clone 时默认的远程仓库名字。 如果你运行 git clone -o booyah，那么你默认的
远程仓库的名字将为 booyah；如果你运行 git clone -b branch-name，那么你默认检出的分支将
为 branch-name，而不是 master。
