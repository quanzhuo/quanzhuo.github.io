关于该仓库
=========
该仓库是我的 GitHub Pages 源码仓库。效果请参看我的 [GitHub Pages](https://quanzhuo.github.io/)。
在 [Gaohaoyang](https://github.com/Gaohaoyang)
的 [GitHub Pages](https://github.com/Gaohaoyang/gaohaoyang.github.io) 的基础上稍加
修改，去掉了不需要的内容。感谢他作出的这个简明实用的博客站点。

博客主要页面
==========
1. 主页：包含最新 5 篇博客的概览。
2. 文章：按照时间排序的文章列表
3. 分类：文章按类别分类
4. 标签：按照标签分类的文章列表
5. 关于：项目，联系方式等介绍
6. 目录：页面滚动时目录固定在屏幕右侧，若目录高度超出屏幕高度，目录产生滚动条。

怎么使用
=======

1. 安装 ruby 和 jekyll 环境
--------------------------
    
这一步和第5步主要是为了让博客系统在本地跑起来，如果不想在本地运行，可以无视这两步，但我还是
强烈建议试着先在本地跑起来，没有什么问题后再推送的 GitHub 上。
    
Windows 用户可以直接使用 RubyInstaller 安装 ruby 环境。后续的操作中可能还会提示安装 
DevKit，根据提示操作即可。安装 jekyll 命令如下：

    gem install jekyll

详情可以查看 [jekyll 官网](https://jekyllrb.com/) 或 
[中文翻译版 jekyll 官网](http://jekyllcn.com/)。 
    
2. 克隆该仓库
------------

    git clone https://github.com/quanzhuo/quanzhuo.github.io.git
        
3. 修改参数
----------
主要修改 _config.yml 中的参数和自己的网站小图 favicon.ico 。

在 _config.yml 文件中：

  1. 修改基本信息，主要用于网站头部：header

    ```yml
    # Site settings
    title: zhuo
    brief-intro: Software Engineer
    baseurl: "" # the subpath of your site, e.g. /blog
    url: "http://quanzhuo.github.io" # the base hostname & protocol for your site
    ```
    
  2. 修改链接信息

    ```yml
    # other links
    #twitter_username: gaohaoyang126
    #facebook_username: gaohaoyang.water
    github_username:  quanzhuo
    email: zhuo.quan@outlook.com
    #weibo_username: 3115521wh
    zhihu_username: quan-zhuo
    #linkedIn_username: gaohaoyang
    dribbble_username:

    description_footer: 学而时思！
    ```
    
  3. 如果你修改好了一切，就可以删除 clone 下来的仓库，只保留工作区。删除工作区中 _posts
     目录中的文件，这是我的博客内容。删除 _drafts 目录下的文件，这是我的草稿文件。然后重新
     在工作区的根目录下初始化一个仓库。
     
4. 写文章
---------
_posts目录下存放文章信息，文章头部注明 layout(布局)、title、date、categories、tags、
author(可选)，如下：

    ---
    layout: post
    title:  "对这个 jekyll 博客主题的改版和重构"
    date:   2016-03-12 11:40:18 +0800
    categories: jekyll
    tags: jekyll 端口 markdown Foxit RubyGems HTML CSS
    author: Haoyang Gao
    ---
    
下面这两行代码为产生目录时使用：

    * content
    {:toc}
    
文章中存在的4次换行为摘要分割符，换行前的内容会以摘要的形式显示在主页Home上，进入文章页不影响。
换行符的设置见配置文件_config.yml的 excerpt，如下：

```yml
# excerpt
excerpt_separator: "\n\n\n\n"
```

接下来就可以使用 markdown 语法写文章。

5. 本地运行
----------
    
    jekyll s
    
在本地访问 localhost:4000 即可看到博客主页。

6. 发布到 GitHub
----------------
没什么问题，推送到自己的博客仓库即可。