---
layout: post
title: "Android init 语言"
categories: Android
tags: init
---

* content
{:toc}

Android init 语言规定了用来编写 /init.rc 文件的语法规则。总共有 5 种语句：Actions，
Commands，Services，Options 和 Imports。




Android init 语言是面向行的，每一行由空白分隔的**符号**（token）组成。 可以使用反斜杠在
一个符号内插入空白。位于双引号内的空白没有分隔符号的作用。如果一个反斜杠后面紧跟一个换行符，
则下一行是该行的延续。

以 # （可以有前导空白） 开头的行为注释。

Actions 和 Services 隐式地声明了一个**小节**（section）。所有的 commands 和 options
都属于前面最近声明的小节。第一个小节前面的 commands 和 options 被忽略。

Actions 和 Services 都有唯一的名字。如果两个 Action 的名字相同，第二个 Action 中的 
commands 被追加到第一个 Action 中。然而，如果两个 Service 的名字相同，则第二个 Service
被忽略并且会有日志记录错误信息。

.rc 文件
--------
init 语言的源文件以 .rc 作为后缀。通常在系统的多个位置存在这种文件。

`/init.rc` 是主要的 .rc 文件，它由 init 进程在执行初期加载。它负责系统的初始化设置。
它导入了文件 `/init.${ro.hardware}.rc` 该文件由供应商提供。

Actions
-------
Actions 是具有名字的命令序列。Action 具有一个触发器，用于决定 Action 什么时候执行。当一个
满足 Action 触发器的事件发生时，该 Action 就被加入到 即将执行的 Action 的队列尾部。队列
中的 Action 按顺序被移除，Action 中的命令按顺序执行。Action 的格式如下：

    on <trigger> [&& <trigger>]*
       <command>
       <command>
       <command>

Services
--------
Services 是由 init 启动的程序，当它们退出时由 init 重启。Service 的格式：

    service <name> <pathname> [ <argument> ]*
       <option>
       <option>
       ...
       
Options
-------
Options 是 Services 的 **修饰符**（modifier）。它们影响 init 如何以及什么时候运行 
service 。

Triggers
--------
Triggers 是用于匹配特殊事件的字符串，用于触发执行一个 Action。

Triggers 又分为事件 trigger 和 属性 trigger。


