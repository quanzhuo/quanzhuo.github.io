---
layout: post
title: "Git 常用命令及选项"
categories: VCS
tags: Git
---

* content
{:toc}

git 是一个流行的开源的分布式版本控制系统。包含了150多个子命令，常用的只有 30 个左右。下面
按照常见的开发工作流介绍 git 在日常工作中的可能使用到的命令以及一些极有帮助的选项。




克隆或者新建仓库
--------------
+ `git clone <repo-url>`
+ `git clone -b <name>` : 检出 name 分支，而不是默认的 master
+ `git init`

配置
--------
git 的配置分为三个等级：system，global 和 local，分别代表系统级配置，用户全局配置，仓库
独立的配置。优先级递增。高优先级的配置会重载低优先级的配置。这三个等级的配置分别通过指定
`--system`， `--global` 和 `--local` 选项进行设定。

+ `git config --global user.name <your-name>`
+ `git config --global user.email <your-email>`     
+ `git config --global alias.st status`
+ `git config --global alias.graph "log --graph --oneline --all --decorate"`
+ `git config --global core.editor vim`
+ `git config --global core.fileMode false`
+ `git config --list`

前两项配置 git 的用户名和邮箱地址，当大家用同一账号工作于同一主机的时候，不要指定
global选项，否则在你提交代码的时候，你的工作成果就成了别人的，或者别人的工作成果成了你的。
应该进入你的仓库，在设置的时候指定 local 选项： `git config --local ...`

跟踪文件、添加修改、检查状态与提交
---------------------
+ `git add <path>`
+ `git add .`
+ `git status`
+ `git commit`
+ `git commit -a`：跳过添加到暂存区的步骤，直接提交
+ `git commit -v`：在提交时显示详细（统一 diff 格式）的文件变化信息
+ `git commit -m <message>`
+ `git commit --amend`：修订提交
+ `git commit --amend --reset-author`：修改提交者信息

检查修改
-------
+ `git diff`：对比工作区与暂存区
+ `git diff --staged | --cached`：对比暂存区与仓库
+ `git diff <commit>`：对比工作区和某一次提交
+ `git diff <path> <path>`：对比两个文件
+ `git diff <commit> <commit> [path...]`：对比两次提交

查看提交日志
-------
+ `git log [path...]`
+ `git log -p -n [path...]`：显示最新的 n 次提交，并列出详细的对比
+ `git log --pretty=fuller`
+ `git log --oneline --graph --decorate --all`
+ `git log --author=<pattern> --committer=<pattern>`
+ `git log --after=<date> --before=<date>`
+ `git log --grep=<pattern>`

移除、移动文件
-------
+ `git rm <file>...`
+ `git mv <src>... <dst>`

撤销操作
-------
+ `git checkout -- <file>...`：取消工作区文件的修改
+ `git reset HEAD <path>...`：取消暂存
+ `git reset --soft <commit>`：重置当前分支指向某次 commit
+ `git reset --mixed <commit>`：并重置暂存区
+ `git reset --hard <commit>`：并重置工作区

远程仓库
-------
+ `git remote -v`：显示远程仓库
+ `git remote show <remote-name>`：显示远程仓库的所有引用
+ `git ls-remote -h -t`：同上
+ `git fetch <remote-repo> <branch-name>`
+ `git push <remote-repo> <src>:<dst>`
+ `git pull <remote-repo>`

分支
---
+ `git branch [-r] [-a]`：列出本地分支，远程跟踪分支，所有分支
+ `git branch <branch-name>`：创建分支
+ `git checkout <branch-name>`：切换分支
+ `git checkout -b <branch-name> [start-point]`：新建分支并切换
+ `git branch --track <branch-name> [start-point]`：创建分支并跟踪远程分支
+ `git checkout -b <branch-name> --track <remote>/<branch-name>`
+ `git branch --set-upstream-to=<upstream> [branch-name]`：设置跟踪分支
+ `git push origin --delete branch-name`：删除远程分支
+ `git merge <branch-name>`

打标签
-----
+ `git tag`
+ `git tag -l <pattern>`：列出
+ `git tag -a -m <message> <tag-name> [commit]`：打 tag
+ `git tag -d <tagname>...`： 删除

其它有用命令
----------
+ `git rev-parse --git-dir`
+ `git push -f`：即使不能快进合并，也强制更新远程仓库的引用，修改提交信息时很有用。

gerrit 代码提交 sop
------------------
1. `git clone ssh://YOU-ACCOUNT@10.195.229.38:29418/QC/platform/frameworks/av`
2. `git checkout -b C5F/dev/MSM89xx/04710/PFAM --track origin/C5F/dev/MSM89xx/04710/PFAM`

    这一步是在本地新建一个工作分支跟踪远程分支并检出。其实，前两步可以合并为：
    `git clone ssh://YOU-ACCOUNT@10.195.229.38:29418/QC/platform/frameworks/av`
    ` -b C5F/dev/MSM89xx/04710/PFAM`。分两步走可以让我们更加清楚自己在干什么。

3. 修改代码
4. `git add; git commit`：添加修改并提交
5. `git push origin HEAD:refs/for/C5F/dev/MSM89xx/04710/PFAM`：推送到远程并等待审核

为什么要加 `refs/for/`？

根据 git 的语法，就上面的例子而言，将本地分支的最新修改推送到远程仓库，需要这样做：
`git push origin HEAD:C5F/dev/MSM89xx/04710/PFAM`，但是对于 gerrit 来说，必须加上
`refs/for/`，这是 gerrit 的特殊语法，加上 `refs/for/` 之后，实际推送过去的分支并没有
合并，而是处于等待 review 的状态。

可以在 [这里](http://10.195.229.38/Documentation/user-upload.html) 找到关于 gerrit
提交代码的详细资料。


忽略文件
-------
一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些
自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建
一个名为 .gitignore 的文件，列出要忽略的文件模式。 来看一个实际的例子：

    $ cat .gitignore
    *.[oa]
    *~

第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。一般这类对象文件和存档文件都是编译过程中
出现的。 第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都
用这样的文件名保存副本。 此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的
文档等等。 要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

文件 .gitignore 的格式规范如下：

+ 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
+ 可以使用标准的 glob 模式匹配。
+ 匹配模式可以以（/）开头防止递归。
+ 匹配模式可以以（/）结尾指定目录。
+ 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。
