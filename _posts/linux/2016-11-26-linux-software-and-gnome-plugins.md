---
layout: post
title:  "好用的 Linux 软件以及 Gnome Shell 扩展"
categories: Linux
tags:  
---

* content
{:toc}

虽然很多软件开发商不对 Linux 平台提供支持，但是开源世界的大神们总有自己的解决方案，而且很多用
起来还不错。 Gnome 桌面的默认安装给人一种粗枝大叶的感觉，看起来不够细腻，但是 Gnome Shell 
提供了很多强大的插件，稍加定制即可满足需求。 我个人更加喜欢 Gnome 桌面，很大一部分原因是觉得 
Gnome 桌面更加正统，我想这也是各大 Linux 发行版本的桌面版本基本上都选择 Gnome 作为默认桌面
的一个原因吧。下面将介绍一些 Linux 上的一些提供生产力的软件以及 Gnome Shell 插件。



介绍软件以及扩展之前，先上一张我的桌面的图片吧。 

![](/asserts/images/linux/desktop.png).

软件
---

+ Visual Studio Code

    微软出品，必属精品。作为程序员，首推 Visual Studio Code。 Visual Studio Code 里面
    有你所需要的编辑器中的所有特性：IntellSence，Plugins，Git...，支持三大主流平台。微软
    是真的开始拥抱开源社区了，前几天还刚刚加入了 Linux 基金会，成为 Linux 基金会的白金会员
    每年贡献 50W 美元会费。

    既然是编辑器，当然绕不过 Vim 和 Emacs，我也是一个 Emacs 使用者。Emacs 是我在命令行下的
    编辑器首选。我最喜欢 Emacs 的行编辑功能。另外 Github 开发的 Atom 是跟 VS Code 差不多
    的一个编辑器，但是根据我的使用，它跟 VS Code 相比，在稳定性，健壮性，以及启动速度上要相差
    不少。

+ insomnia

    一个跨平台的 Rest API 调试客户端。界面漂亮。它的站点请戳
    [这里](https://insomnia.rest/)。

+ NixNote

    Evernote 的 Linux客户端，由开源社区维护，使用 Evernote 官方提供的开发者 API 开发。
    我的重要的资料都放在 Evernote 上，现在已经 离不开 Evernote 了。 NixNote 基本上提
    供了 Evernote 具有的所有功能。跟 Gnome 桌面配合的非常完美，目前发现的唯一美中不足的地方
    是在文章中调整文字颜色以及背景颜色的时候有一些小问题。目前仍然在活跃开发中，上次更新时间
    是： 2016-10-24 ，它的官方站点在
    [这里](https://sourceforge.net/projects/nevernote/)。

+ synapse

    是一个用于搜索计算机上任何东西的工具，十分好用。按下 Ctrl + space 键弹出搜索对话框。各大
    发行版本的软件仓库中都提供有，直接安装即可。

+ 搜狗输入法
+ 网易云音乐
+ 福昕阅读器

    以上三款软件大家耳熟能详的软件也都有 Linux 客户端。Linux 版本的搜狗输入法虽然没有 
    Windows 版本的智能，但是目前绝对是 Linux 平台上最好用的中文输入法。搜狗输入法和网易云
    音乐只支持 Ubuntu，福昕阅读器是真正的全平台软件，支持所有的 Linux 发行版本、Mac、
    Windows、Android、iOS、Windows Phone 8、Windows 10 mobile，福昕阅读器是专注成就
    成功的最好案例。

+ vlc

    不用赘述，万能的播放器。

+ Guake

    Guake 是我最喜欢的 Linux 下拉终端。在 Linux 上需要使用终端的地方特别多，使用下拉终端能
    极大的提高生产力。虽然 Gnome Shell 中有下拉终端插件，但是 Guake 的稳定性，健壮性是其他
    下拉终端无法相比的。还支持丰富的设置项。我习惯把下拉终端的快捷键设置为反引号，也就是 Tab
    键上面的那个按键，需要使用的时候，中指一伸即可弹出。

+ terminator

    一个桌面版本的终端复用工具，直接使用 apt、yum、dnf 即可安装。

+ gnome-tweak-tool

    Gnome 桌面的微调工具。

+ Anydesk

    跨平台的远程桌面工具。安装方便，传输迅速，几乎不用配置。下班后如果有急需完成的工作，可以
    使用 Anydesk 连接到你的办公电脑上，不用去办公室啦。

+ cmus

    一个基于 ncurses 的命令行音乐播放器，用起来十分方便。

+ everpad

    这个也是一个 Evernote 客户端，不过是针对 Ubuntu 的 Unity 桌面的。跟 Unity 的 Dash
    集成到一块儿。使用 Unity 桌面的用户可以试一试。

+ gnome-pie

    Gnome 环境下的一个可视化的程序启动器。定制性很强。对于某些使用脚本作为启动文件的程序，使用
    gnome-pie 为之添加一个启动入口是十分有用的。而且支持自定义应用程序图标。使用快捷键调出，
    十分炫酷。

Gnome Shell 扩展
---
下面介绍一些 Gnome Shell 的扩展，这只是一些方便我使用的一些扩展。Gnome Shell 有多达数百个
扩展，更多扩展可以到[这里](https://extensions.gnome.org/#)看看。

+ Dash to Dock

    这是一个适用于 Gnome Shell 的 docker。可以将 Gnome Shell 的 Dash 变成一个在其他平台
    常见的 docker，也更加方便使用，并且可以定义 docker 的大小，位置等扥。

+ topicons plus

    该插件将 Gnome Shell 传统的左下角通知栏移动到桌面顶部的的状态栏。它是我第一次使用 Gnome
    就想要的扩展。

+ Screenshot tools

    快捷的截图工具，安装之后在顶部状态栏有一个按钮，方便截屏，十分实用的一个插件。

+ Remove Dropdown Arrows

    Gnome Shell 默认情况下顶部状态栏图标的右下角会有一个向下的小箭头，该扩展移除这个小箭头。
+ Straight Top Bar:

    移除窗口的圆角，使桌面看起来更加扁平化，更加符合现在的主流审美。

+ Frippery Move Clock

    将状态栏中间的时钟移动到其他地方。

+ Dynamic Top Bar

    动态的顶部状态栏，当没有最大化窗口的时候，顶部状态栏是透明的，当有最大化窗口的时候，顶部
    状态栏变成系统的背景颜色。

+ Glassy Gnome

    可以改变 Gnome 桌面窗口的透明度。

+ Openweather

    天气预报插件。
