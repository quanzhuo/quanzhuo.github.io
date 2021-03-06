---
layout: post
title: "多人打 Patch 的一种协作方式"
categories: VCS
tags: Patch
---

* 目录
{:toc}

在向 C5 中上 10-05 和 11-05 Google 安全 Patch 的过程中，发现了一些问题。
在此总结一下多人协作打大量 Patch 的一种有效的协作方式。




现有的协作方式
----
Google 安全公告上每月发布一次，每一次大概会有 60 个 Patch 需要上。现在
的协作流程如下：

1. 60 个 Patch 分给个人，大概每个人 10 个左右，大家分别去寻找自己的 Patch
2. 在一台服务器上取一份完整的代码，所有的人都在这份代码上打上自己分到 Patch
3. build 通过之后，统一提交到 git

存在的问题
----
1. 如果两 Patch 修改了同一个文件，有可能两个人同时去修改同一个文件
2. 同一台服务器上通常配置了全局的用户名和邮箱，打进去的 Patch 提交者都是
   同一个人，虽然可以通过在不同的仓库中配置 local 用户名和邮箱，但是 Google 
   安全公告中的漏洞 90% 都是针对 kernel 的。
3. 最主要的问题：如果某一个 Patch 上错（几率较大）导致编译失败，无法知道该
   Patch 是谁打的，也就无法知道 CVE 编号，从而要花费大量的时间去一个一个
   排查问题
   
一个有效的协作方式
----
1. 每人将自己分到的 Patch 打入到自己本地仓库中
2. 验证通过之后通过 `git format-Patch` 针对自己对代码所做的改变做出 Patch
3. 在工作站上取一份代码作为中央仓库，每个人按照顺序使用 `git am` 依次打入自己
   生成的 Patch，打之前，通过 `git config --local` 配置一下个人信息
4. 整编通过，刷机没问题之后，每个人在自己的本地仓库中将代码推送到 git 服务器

比较
----
跟第一种协作方式相比，第二种方式似乎更加麻烦：

+ 每个人都有自己的个人仓库
+ 每个人需要重新针对已经打入的 Patch 生成 Patch
+ 需要按照顺序将生成的 Patch 打入公共仓库

其实

+ 每个人在服务器上基本上都有代码，甚至有多份，基本上不需要重新去取一份
+ 使用 `git format-Patch` 针对自己对代码所做的更改生成 Patch 基本上是秒级别的
+ 使用 `git am` 应用 Patch 也是秒级别的，而且这两步不会出错

带来的改变

+ 自己打上的 Patch 不会成为别人的功劳
+ 不会跟别人冲突
+ 因为经过在本地仓库中的验证，整编的时候基本上不会出错
+ 如果整编的时候出错，可以很快定位到相关 Patch 的 CVE 编号，快速定位问题。
+ 每个人本地仓库中的提交历史较短，发现错误后纠正错误相对容易

2016-12-24 号更新
----
上面所说的现有的协作方式中存在问题都是因为目前所有人都使用同一账号引起的。
多人使用同一账号在工作站上工作极不规范，会带来很多问题。正确的做法应该是，为
每人都在工作站上创建一个账号，这样就可以规避本文中提到的各种问题。可以参考这篇文章：
[在 Linux 上使用 Group 和 ACL 进行团队工作](https://quanzhuo.github.io/2016/12/12/group-and-acl/)。
