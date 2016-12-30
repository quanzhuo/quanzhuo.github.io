---
layout: post
title:  "AWK 语言"
categories: Linux
tags: awk
---

* content
{:toc}

awk 是处理文本的利器，使用 awk 可以完成很多复杂的文本处理工作。awk 工具提供了编程语言
中具有的程序流程控制，字符串处理，随机数生成，数学运算等功能。因此 awk 被称之为一门
语言，可见 awk 工具的强大。'awk' 这个名字是取它最初的三位开发者名字的首字母而来。awk 
可以在 Linux，OSx，Windows 上使用。



## 概述

awk 程序由一系列的 `pattern {action}` 和函数定义组成。较短的 awk 程序直接在命令行中
输入，通常程序文本被括在单引号内，以免被 shell 特殊解释；如果程序较长，可以通过 -f 选
项在文件中读取。要处理的文本名直接在命令行中读取，如果在命令行中没有提供文件名，则处理
标准输入。输入被根据 RS 变量划分为记录（records）。RS 变量默认等于 "\n"，因此，记录和
行是同义词。每一个记录都要和每一个 pattern 尝试进行匹配。如果匹配，pattern 后面的 
{action} 就被执行。

## 编程结构

AWK 程序可以是一系列的 `pattern {action}` 对，和用户函数定义。 awk 语言是面向行的，
每一个 action 必须开始于 pattern 所在的那一行。`pattern {action}` 对可以跨越多行。

pattern 可以是：

	BEGIN
	END
	expression
	expression , expression

pattern 和 {action} 两个中可以忽略一个。如果没有提供 {action}，默认为 { print }。如
果忽略了 pattern，就是为是匹配的。 BEGIN 和 END 这两个 pattern 必须提供一个 action。

语句由换行符，分号，或者换行符和分号分隔。语句块，比如说 actions 或者循环体需要使用大
括号括起来，就像在 C 语言中一样。语句块中的最后一条语句后面不需要跟分隔符。空白行没有
意义；空语句由分号结尾。较长的语句可以通过反斜线使之书写在多行。如果一条语句在逗号，左
大括号，&&，||，do，else，或者 if，while，for 语句，函数定义的右小括号之后换行，那么
可以不使用反斜线。注释以 # 开始一直持续到行尾，但是不包含换行符。

以下的语句控制语句块中的程序流。

	if ( expr ) statement
	if ( expr ) statement else statement
	while ( expr ) statement
	do statement while ( expr )
	for ( opt_expr ; opt_expr ; opt_expr ) statement
	for ( var in array ) statement
	continue
	break


## 数据类型，转换和比较

有两种基本数据类型，数字和字符串。数值常量可以是整数，像 -2，小数，像 1.08，或者使用
科学计数法表示，比如 -1.1e4 或者 .28E-3。所有的数值表示和计算在内部都使用浮点数。举个
例子，表达式 0.2e2 == 20 为 true，true 是由 1.0 表示的。

字符串常量由双引号括起来。例如："This is a string with a newline at the end.\n"

通过对换行符进行转义，字符串可以跨越多行。接受以下转义序列

	\\        \
	\"        "
	\a        alert, ascii 7
	\b        backspace, ascii 8
	\t        tab, ascii 9
	\n        newline, ascii 10
	\v        vertical tab, ascii 11
	\f        formfeed, ascii 12
	\r        carriage return, ascii 13
	\ddd      1, 2 or 3 octal digits for ascii ddd
	\xhh      1 or 2 hex digits for ascii  hh

实际上有三种数据类型；第三种是数字和字符串类型，它同时具有一个数字值和一个字符串值。

表达式的类型是由它所处的上下文决定的，如果需要，就会发生自动类型转换。举个例子：

	y = x + 2; z= x "hello"

y 将会是数字类型。如果 x 不是数字，那么 x 中读取到的值将会被转化为数字，然后加上 2 ，
赋给 y。z 将会是字符串类型，x 如果不是字符串类型，将会被转化为字符串类型然后和 "hello"
字符串连接起来赋给 z。**请注意**：x 的类型以及值并没有发生变化。 字符串表达式转化为数
字按照 c 库函数 `atof` 的规则进行。

在一个需要布尔值的上下文上，例如 `if ( expr ) statement`，如果 expr 是一个字符串表达
式，只有在 expr 为空串的时候，才为 false，否则为 true；如果 expr 是数字值，只有当 
expr 为 0 时，转换为 false，否则转换为 true。


## 正则表达式

在 AWK 语言中，记录，域和字符串经常被测试是否匹配一个正则表达式。正则表达式被两个斜线
'/' 括起来。对于下面的 AWK 表达式

    expr ~ /r/

如果 expr 匹配 r，那么其值为 1，如果不匹配，其值为 0。`~` 为匹配操作符， `!~` 为不匹
配操作符。作为 pattern-action 对，

    /r/ { action }   and   $0 ~ /r/ { action }

是一样的，对于每一个匹配 r 的输入记录，都将执行 action。实际上， /r/ 和 $0 ~ /r/ 是
等价的。

AWK 使用和 egrep 一致的扩展正则表达式语法

## 记录和域

记录每次读入一个，存储在变量 $0 中。记录会被划分为域，并且分别赋值给变量 $1，$2，...
$NF。内建变量 NF 等于划分的域的数目，NR 和 FNR 加 1。大于 $NF 的域被设置为 ""。

给 $0 赋值，会导致域和 NF 被重新计算。给 NF 或者一个域赋值或导致 S0 被重新构建。如果
给大于 NF 的域赋值，会增加 NF，并且导致 S0 被重新构建。

域中存储的数据类型为字符串，除非整个域都是数字格式，此时该域的数据类型为 “数字和字符串”
例如：

	echo 24 24E |
	awk '{ print($1>100, $1>"100", $2>100, $2>"100") }'
	0 1 1 1

在上面的例子中，$0 和 $2 是字符串，$1 是数字和字符串类型。print 语句中，第一个比较是
数值比较，第二个是字符串比较，第三个是字符串比较（100 会被转化为 "100"），最后一个也是
字符串比较。

域的索引并不是常量。任何 awk 语言中的合法表达式都可以放在 $ 后面来引用一个域。表达式的
值指定了域的索引。如果表达式的值是字符串，则会被转化为数字。例如：

	awk '{for(i=1;i<=NF;i++) 
			print $i }'

上面的代码将文件中每一行的每一个域打印在一行上。

## 表达式和操作符

	assignment          =  +=  -=  *=  /=  %=  ^=
	conditional         ?  :
	logical or          ||
	logical and         &&
	array membership    in
	matching       ~   !~
	relational          <  >   <=  >=  ==  !=
	concatenation       (no explicit operator)
	add ops             +  -
	mul ops             *  /  %
	unary               +  -
	logical not         !
	exponentiation      ^
	inc and dec         ++ -- (both post and pre)
	field               $

## 数组

## 内建变量

+ ARGC      number of command line arguments.
+ ARGV      array of command line arguments, 0..ARGC-1.
+ CONVFMT   format for internal conversion of numbers to string, 
  initially = "%.6g".
+ ENVIRON   array indexed by environment variables.  An environment string, 
  var=value is stored as ENVIRON[var] = value.
+ FILENAME： 当前正在处理的输入文件的名字
+ FNR：当前输入文件的当前记录编号（行），该变量跟 NR 变量的区别是，每当读取一个新的文件
  时，该变量都将被重置。而无论读取多少个输入文件，NR 变量都不会被重置。
+ FS：域分隔符，默认为：空格，水平制表符，换行符。可被重新制定为一个正则表达式。
+ NF：一条记录中域的个数，对域的引用如果大于该数字，得到空字符串（""）。
+ NR：当前的记录索引，从 1 开始。
+ OFMT      format for printing numbers; initially = "%.6g".
+ OFS       inserted between fields on output, initially = " ".
+ ORS       terminates each record on output, initially = "\n".
+ RLENGTH   length set by the last call to the built-in function, match().
+ RS：记录分隔符，默认为 "\n"，如果你想要改变该变量，一般在与 BEGIN 配对的 {action}
    中更改，例如：

		awk 'BEGIN { RS = "u"} 
		 	 { print $0 }' mail-list

	记录分隔符也可以是一个正则表达式。

+ RSTART    index set by the last call to match().
+ SUBSEP    used to build multiple array subscripts, initially = "\034".

## 内建函数

## 输入和输出

## 用户定义函数

## 分隔字符串，记录和文件

## 多行记录

## 程序执行流程

## 例子