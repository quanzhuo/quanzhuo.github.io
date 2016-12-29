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

+ \<sha1\>, e.g. dae86e1950b1277e545cee180551750029cfe735, dae86e

    完整的 SHA-1 ID（4O 字节的十六进制字符串），或者一个在仓库中可以唯一代表本次提交的
    前导子串都可以指代本次提交。

+ \<describeOutput\>, e.g. v1.7.4.2-679-g3bee7fb

    git describe 命令的输出；包含一个 tag，后跟一个连字符，这一个提交数目，一个连字符，
    一个字符 g，最后是一个简写的对象ID。

 + \<refname\>, e.g. master, heads/master, refs/heads/master

+ @：一个单独的 @ 代表 HEAD

+ 

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

+ \<rev\>：代表从 \<rev\> 可以追踪到的提交（rev 的所有祖先）。

+ ^\<rev\>：不包含从 \<rev\> 可以追踪到的提交。

+ \<rev1\>..\<rev2\>

    包含从 \<rev2\> 可以追踪到的所有提交，但是不包含从 \<rev1\> 可以追踪到的提交。

+ \<rev1\>...\<rev2\>

    包含从 \<rev1\> 或者 \<rev2\> 可以追踪到的提交，但是不包含从两者都可以追踪到的提
    交。这和上一种范围表示法容易混淆。上面的是两个点，而这个是三个点。举例说明，在上面
    的图示中，D..F 表示的提交范围只有F，而 D...F，表示的提交范围有 D 和 F。

+  \<rev\>^@, e.g. HEAD^@

    代表 \<rev\> 所有的父提交，也就是从它的父提交可以追踪到的所有提交，但是不包含它
    自己。

+ \<rev\>^!, e.g. HEAD^!

