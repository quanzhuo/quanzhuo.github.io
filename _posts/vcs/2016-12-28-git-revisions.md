---
layout: post
title: "Git 版本及版本范围表示法"
categories: VCS
tags: Git
---

* content
{:toc}

很多 Git 命令都使用 revision（修订版本）作为参数。根据不同的命令，有时候 revision 参
数代表一个特定的提交，有时候代表某一个提交可以追踪到的所有的父提交（比如 git log）。在
后面的这种情况中，一般也可以提供一个版本范围作为参数。另外，有的命令（例如 git show）
使用 revision 参数代表其他对象，如 blobs 或者 tree 对象。



## 指定 revision

一个 revision 参数 *\<rev\>* 在通常情况下（但并不是所有情况下）代表一个提交对象
（commit）。使用一种称为 扩展 SHA-1（extended SHA-1）的语法。下面是各种各样的使用
revision 的语法。

+ `<sha1>`, e.g. dae86e1950b1277e545cee180551750029cfe735, dae86e

    完整的 SHA-1 ID（4O 字节的十六进制字符串），或者一个在仓库中可以唯一代表本次提交的
    前导子串都可以指代本次提交。

+ `<describeOutput>`, e.g. v1.7.4.2-679-g3bee7fb

    git describe 命令的输出；包含一个 tag，后跟一个连字符，这一个提交数目，一个连字符，
    一个字符 g，最后是一个简写的对象ID。

 + `<refname>`, e.g. master, heads/master, refs/heads/master

    符号引用。例如：master 通常代表 refs/heads/master 引用指向的提交对象。如果你的仓
    库中同时存在 heads/master 和 tags/master，要想代表 master 分支，可以显式指定为：
    heads/master。如果一个引用名出现了二义性，那么 Git 按照下面的规则处理，使用找到的
    第一个匹配。

    1. 如果存在 `$GIT_DIR/<refname>`，则使用之
    2. 否则，查找 `refs/<refname>`
    3. 否则，查找 `refs/tags/<refname>`
    4. 否则，查找 `refs/heads/<refname>`
    5. 否则，查找 `refs/remotes/<refname>`
    6. 否则，查找 `refs/remotes<refname>/HEAD`

+ `@`：一个单独的 @ 代表 HEAD

+ `<refname>@{<date>}`, e.g. master@{yesterday}, HEAD@{5 minutes ago}

    一个引用名，后跟一个 @ 符号，在后面是一个括在大括号里面的日期（例如：{yesterday}，
    {1 month 2 weeks 3 days 1 hour 1 second ago} 或者 {1979-02-26 18:30:00}）代表
    该引用在该段时间之前的值。这种用法只能用于引用上面，不能用于一个确切的 commit ID，
    并且该引用必须在 $GIT_DIR/logs/<ref> 中存在有 log 记录。注意，这种用法只会查看你
    的本地仓库在给定时间点的状态。如果你要查看在某一时间段内的状态，请使用 `--since`
    和 `--until` 参数。

+ `<refname>@{<n>}`, e.g. master@{1}

    一个引用名后面跟着一个 @ 后缀，然后是括在大括号中的一个数字，例如（{1}，{15}）。
    可以代表该引用之前第 n 次的值。例如，`master@{1}` 代表 master 之前的值。而
    `master@{5}` 则代表 master 前面第 5 次的值。这种用法只能用于引用上，并且该引用
    必须具有引用日志（`$GIT_DIR/logs/<refname>`）。

+ `@{<n>}`, e.g. @{1}

    如果省略了引用部分，则代表当前分支。例如：如果你当前在分支 blabla 上，那么 @{1}
    就和 blabla@{1} 是一个意思。

+ `@{-<n>}`, e.g. @{-1}

    @{-<n>}， 这种表示法，代表之前第 n 次检出的分支或者提交。

+ `<rev>^`, e.g. HEAD^, v1.5.1^0

    在一个 revision 后面加上一个 `^` 后缀可以用来指代 revision 的直接父提交。`^<n>`
    代表第 n 个父提交（例如， <rev>^ 和 <rev>^1 是等同的）。`<rev>^0` 代表 rev 自己。
    当 `<rev>` 是一个 tag 的名字时，`<rev>^0` 表示该 tag 对象对应的提交对象。

+ `<rev>~<n>`, e.g. master~3

    在一个 revision 后面加上一个 `~<n>` 后缀代表 revision 的第 n 个祖先提交。

+ `:/<text>`, e.g. :/fix nasty bug

    一个冒号，后跟一个斜杠，在后面是一串文本。这串文本会被当做正则表达式对待。整个
    `:/<text>` 代表提交信息匹配正则表达式的最新一次提交。正则表达式可匹配提交信息的任
    何部分。如果要匹配提交信息的开头，可以使用 `:/^foo`。

+ `<rev>:<path>`, e.g. HEAD:README, :README, master:./README
+ `:<n>:<path>`, e.g. :0:README, :README

下面是一个图示，展示了各个概念：

    G   H   I   J
     \ /     \ /
      D   E   F
       \  |  / \
        \ | /   |
         \|/    |
          B     C
           \   /
            \ /
             A
                           
    A =      = A^0
    B = A^   = A^1     = A~1
    C = A^2  = A^2
    D = A^^  = A^1^1   = A~2
    E = B^2  = A^^2
    F = B^3  = A^^3
    G = A^^^ = A^1^1^1 = A~3
    H = D^2  = B^^2    = A^^^2  = A~2^2
    I = F^   = B^3^    = A^^3^
    J = F^2  = B^3^2   = A^^3^2


## 指定范围

那些需要遍历历史提交的命令（如 git log）会对一系列的提交进行操作，并不仅仅针对某一次提
交。对于这些命令来说，如果只给它们提供一个 revision 参数，那么意思是指代沿着该 
revision 的父提交链可以追踪到的所有提交。

如果要排除从某一次提交可以追踪到的提交，只需要在该提交的前面加上前缀 `^`。例如：
`^r1 r2` 指代从 r2 可以追踪到的所有提交但是不包含从 r1 可以追踪到的所有提交。这种表示
方法非常常见，因此对于 `^r1 r2` 来说有一个简写表示法：`r1..r2`。可以省略一个 revision，
省略的 revision 默认是 HEAD。

相似的表示法：`r1...r2` 可以叫做 r1 和 r2 之间的差集（数学），就是从 r1 可以追踪到的
或者是从 r2 可以追踪到的，但是不包含从 r1 和 r2 都可以追踪到的。

+ `<rev>`：代表从 `<rev>` 可以追踪到的提交（rev 的所有祖先）。

+ `^<rev>`：不包含从 `<rev>` 可以追踪到的提交。

+ `<rev1>..<rev2>`

    包含从 `<rev2>` 可以追踪到的所有提交，但是不包含从 `<rev1>` 可以追踪到的提交。

+ `<rev1>...<rev2>`

    包含从 `<rev1>` 或者 `<rev2>` 可以追踪到的提交，但是不包含从两者都可以追踪到的提
    交。这和上一种范围表示法容易混淆。上面的是两个点，而这个是三个点。举例说明，在上面
    的图示中，D..F 表示的提交范围只有F，而 D...F，表示的提交范围有 D 和 F。

+  `<rev>^@`, e.g. HEAD^@

    代表 `<rev>` 所有的父提交，也就是从它的父提交可以追踪到的所有提交，但是不包含它
    自己。

+ `<rev>^!`, e.g. HEAD^!

下面是一些例子：

    D                G H D
    D F              G H I J D F
    ^G D             H D
    ^D B             E I J F B
    B..C             C
    B...C            G H D E B C
    ^D B C           E I J F B C
    C                I J F C
    C^@              I J F
    C^!              C
    F^! D            G H D F
