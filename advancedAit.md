---
title: 'github笔记：git进阶及实用案例'
date: 2018-01-22 20:23:13
categories: github 
tags: [github] 
---

### 写在前面
最近一段时间一直在尝试换工作，感觉自己真是弱爆了，挫败感十足。痛定思痛，就是

```
梦想 - 实力 = 行动
```
好吧，既然选了这条折腾的路，打鱼不成，还是退而结网吧。

今天，先从git基础应用知识整理开始。

### GIT基础篇

github申请、安装及基础使用，这个之前整理过，想见[这里。](https://www.steve-yuan.com/2017/12/16/howToUseGithub/)


### GIT 进阶篇

#### branch 分支

branch的本意是树枝，这里是分支，指的是repo从初始commit到当前commit的一个串。每个github repo都有一个默认分支，叫做master。

![](https://farm5.staticflickr.com/4648/26259667448_9766cde2da_o.jpg)

<!--more-->

##### branch 基础操作

```
git checkout feature1   //创建一个名为feature1的branch

git checkout feature1  //切换到feature1 分制

git branch -d feature1   //删除feature1分支
```

需要注意的是：

1. HEAD 指向的 branch 不能删除。如果要删除 HEAD 指向的 branch，需要先用 checkout 把 HEAD 指向其他地方。
2. 删除 branch 的操作也只会删掉这个引用，并不会删除任何的 commit。
3. 没有被合并到 master 过的 branch 在删除时会失败，如果需要强行删除，需要使用如下命令：

```
git branch -d feature1
```

##### Feature Branching：最流行的工作流
1. 每个新功能都新建一个 branch 来写；

2. 写完以后，把代码分享给同事看；写的过程中，也可以分享给同事讨论。另外，借助 GitHub 等服务提供方的 Pull Request 功能，可以让代码分享变得更加方便；

3. 分支确定可以合并后，把分支合并到 master ，并删除分支。

**实现步骤：**

###### 1）创建分支，编写代码，提交

```
// 假设要开发一个books的新功能
git checkout -b books   //创建并切换到boos分支
git push origin books   //提交代码到books分支
```

###### 2）代码审阅

```
//假设是另一个同事在审阅
git pull
git chekcout books
```

###### 3）合并分支

```
git checkout master
git pull # merge 之前 pull 一下，让 master 更新到和远程仓库同步
git merge books

```

###### 4）提交合并结果并删除books分支

```
git push
git branch -d books
git push origin -d books # 用 -d 参数把远程仓库的 branch 也删了
```

#### git add 

git add 用来将需要改动的内容添加到缓存区，这里有两个使用技巧：

1. add 后面加个点 "."：直接把工作目录下的所有改动全部放进暂存区
2. add 添加的是文件改动，而不是文件名。假定在使用 git add a.txt 后，又对a.txt进行了更改，后来的更改并不会自动放入缓存区，需要再添加一遍。

#### git log

```
git log用来查看历史记录，常用参数如下：

git log -p   //查看commit 的每一行改动，所以很适合用于代码 review

log --stat   //查看大致更改内容，例如什么文件更改了，更改有几处

git show 5e68b0d8   //查看具体的commit，后面的这串数字是commit的SHA-1码的前8位；

git show 5e68b0d8 shopping\ list.txt   //查看某个commit下的指定文件

git diff --staged    //比对暂存区和上一条提交，与git diff --cached完全等价，可以换用
```

### GIT 高级篇

#### 问题一：刚刚提交的代码，发现写错了怎么办？

**基本思路：**commit -—amend会把当前 commit 里的内容和暂存区（stageing area）里的内容合并起来后创建一个新的 commit，用这个**新的 commit 把当前 commit 替换掉。**

##### 第一步：修改错误文档 比如a.txt
##### 第二步：git add a.txt
##### 第三步：git commit --amend


#### 问题二：更进一步，如果要修改的不是最新的commit，而是之前的commit（吃饱了闲的），应该怎么做？

##### 第一步：git log 查询具体是哪一个commit
##### 第二步：开启交互式 rebase 过程，有两种方式：

git rebase -i HEAD^^   // 在 commit 的后面加一个或多个 ^ 号，可以把 commit 往回偏移，偏移的数量是 ^ 的数量，这里指的是HEAD 所指向的 commit 往前数两个 commit。

git rebase -i HEAD～5    //在 commit 的后面加上 ~ 号和一个数，可以把 commit 往回偏移，偏移的数量是 ~ 号后面的数。例如：HEAD~5 表示 HEAD 指向的 commit往前数 5 个 commit。

##### 第三步：选择 需要更改的commit ，将pick改为edit，之后推出编辑界面

![](https://farm5.staticflickr.com/4721/26259670798_68fba6b81f_o.jpg)

##### 第四步：修改写错的 commit

```
git add 笑声
git commit --amend
```

##### 第五步：继续 rebase 过程

```
git rebase --continue
```

git rebase的详细用法[参见这里](https://blog.yorkxin.org/2011/07/29/git-rebase)

#### 问题三：之前提交的commit太烂，如何直接删除？

删除最近提交的commit，可以用git reset

```
git reset --hard 目标commit  //这里可以用HEAD^ 来删除最近一条的commit

```

#### 问题四：想删除的不是最近一条commit，而是之前的某一条，应该怎么做？

还是使用rebase -i 的方式来删除

```
git log   //查询commit历史记录
git rebase -i HEAD^^     //找到要删除倒数第二条，之后直接删除掉需要删除的那一行
```

#### 问题五：当你手头有一件临时工作要做，需要把工作目录暂时清理干净，处理完临时工作再切换回来，这个要怎么实现？

stash 指令可以帮你把工作目录的内容全部放在你本地的一个独立的地方，它不会被提交，也不会被删除，你把东西放起来之后就可以去做你的临时工作；完成临时工作后再切换回来，恢复之前的进度

```
git stash   // 没有add的文件不会被stash，如果所有的文件一起，需要用git stash -u，-u表示 --include-untracked的简写

git checkout fearture1 //假定原来的工作再feature1分支

git stash pop
```

#### 问题六：误删了commit需要找回，应该怎么做？

git reflog   //reflog 是 "reference log" 的缩写，用来查看 Git 仓库中的引用的移动记录

![](https://farm5.staticflickr.com/4625/40100633492_68c5336a9f_o.jpg)

从图中可以看出，HEAD 的最后一次移动行为是「从 branch1 移动到 master」。而在这之后，branch1 就被删除了。所以它之前的那个 commit 就是 branch1 被删除之前的位置了，也就是第二行的 c08de9a。

```
git checkout c08de9a
git checkout -b branch1

```
这样，之前误删的commit就回来了。


