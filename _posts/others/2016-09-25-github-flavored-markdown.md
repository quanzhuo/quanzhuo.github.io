---
layout: post
title:  "GitHub 风格 Markdown 语法"
categories: Markdown
tags:  GitHub Markdown
---

* content
{:toc}

GitHub 支持标准 Markdown 语法，并且在标准 Markdown 语法的基础上添加了一些特有的语法。
下面简单总结一下 GitHub 支持的额外的 Markdown 语法。详细内容可见
[此处](https://help.github.com/articles/about-writing-and-formatting-on-github/)。
基础 Markdown 语法参考
[另外一篇博文](https://quanzhuo.github.io/2016/08/19/markdown-syntax/)。




### 删除线

GitHub 支持从文本中间划过的删除线。在要删除的文本的开头和结尾各加上两个波浪号即可。
例如下面的文本：

    ~~This was mistaken text~~

具有这样的效果：

~~This was mistaken text~~

### 高亮代码块

只需要在代码块的开始和结尾处添加三个反引号即可达到与标准 Markdown 中代码块一样的语法。
要想语法高亮，需要在开头的三个反引号后面加上语言类型。注意：语言类型标识符
**必须是小写**。Github 推荐在代码块的开始和结束处加上一个空行，这样是原始代码更加好看。
例如下面的代码块格式化语法：

    ``` c
    #include <stdio.h>

    int main(int argc, char const *argv[]) {
      printf("Hello World\n");
      return 0;
    }
    ```

会显示为：

``` c
#include <stdio.h>

int main(int argc, char const *argv[]) {
  printf("Hello World\n");
  return 0;
}
```

### 任务列表

在列表条目前加上一对儿方括号： `[ ]` 即可创建任务列表。使用中间带 `x` 号的方括号标识该
任务完成。任务列表可以在 GitHub 的评论中和 Markdown 文件中使用。例如，下面的三行文本：

    - [x] Finish my changes
    - [ ] Push my commits to GitHub
    - [ ] Open a pull request

会显示为

- [x] Finish my changes
- [ ] Push my commits to GitHub
- [ ] Open a pull request

使用表情
-------
可以通过使用 `:表情代码:` 来添加表情

    @octocat :+1: This PR looks great - it's ready to merge! :shipit:

@octocat :+1: This PR looks great - it's ready to merge! :shipit:

支持的表情和表情代码可以在
[这里](http://www.webpagefx.com/tools/emoji-cheat-sheet/)找到。

表格
---
可以在 comments, issues, pull requests, and wikis 中使用表格。使用管道符号和连字符来
创建表格。

    | First Header | Second Header |
    |--------------|---------------|
    | content cell | content cell  |
    | content cell | content cell  |

| First Header | Second Header |
|--------------|---------------|
| content cell | content cell  |
| content cell | content cell  |

连字符用于创建每一列的表头。表格单元的宽度可以任意，必须要每一个单元都严格对齐。用于标
识表头的连字符必须最少三个。在表格中依然可以使用其它的格式语法：链接，代码快，粗体，
斜体等。

还可以在标识表头的连字符的左边，右边，两边使用冒号来标识该列，左对齐，右对齐或者中间
对齐。例如：

    | Left-aligned | Center-aligned | Right-aligned |
    | :---         |     :---:      |          ---: |
    | git status   | git status     | git status    |
    | git diff     | git diff       | git diff      |

| Left-aligned | Center-aligned | Right-aligned |
| :---         |     :---:      |          ---: |
| git status   | git status     | git status    |
| git diff     | git diff       | git diff      |

参考
---
+ [About writing and formatting on GitHub](https://help.github.com/articles/about-writing-and-formatting-on-github/)
+ [Mastering Markdown](https://guides.github.com/features/mastering-markdown/)
+ [Markdown Tutorial](http://www.markdowntutorial.com/)
