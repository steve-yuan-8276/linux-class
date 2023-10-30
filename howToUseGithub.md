---
title: 'git折腾笔记：GitHub 使用简易指南'
date: 2017-12-16 20:53:24
categories: github 
tags: [github, bash, ssh] 
---

### 创建新仓库

创建新文件夹，打开，然后执行以下创建新的 git 仓库。

```
git init
```

### 克隆仓库

执行如下命令以创建一个本地仓库的克隆版本：

```
git clone /path/to/repository 
```

如果是远端服务器上的仓库，你的命令会是这个样子：

```
git clone username@host:/path/to/repository
```
<!--more-->

### 工作流

你的本地仓库由 git 维护的三棵“树”组成：

* 第一个是你的工作目录，它持有实际文件；
* 第二个是 缓存区（Index），它像个缓存区域，临时保存你的改动；
* 第三个是 HEAD，指向你最近一次提交后的结果。


### 添加与提交

#### 第一步：

```
git add <filename>
git add *
```

#### 第二步：

```
git commit -m "代码提交信息"
```
现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

#### 第三步：

```
git push origin master
```

可以把 master 换成你想要推送的任何分支。 

### 分支

创建仓库的时候，默认分支是master；可以手动创建其他分支：

#### 1. 创建一个叫做“feature_x”的分支，并切换过去：

```
git checkout -b feature_x
```

#### 2. 切换回主分支：

```
git checkout master
```

#### 3. 再把新建的分支删掉：

```
git branch -d feature_x
```

#### 将分支推送到远端仓库（只有当分支被push到仓库，别人才能看到）

```
git push origin <branch>
```

### 合并分支

要合并其他分支到你的当前分支（例如 master），执行：

```
git merge <branch>
```

自动合并并非次次都能成功，并可能导致冲突（conflicts）。 这时候就需要你修改这些文件来手动合并冲突。

在合并改动之前，也可以使用如下命令查看：

```
git diff <source_branch> <target_branch>
```

### 标签

执行如下命令以创建一个叫做 1.0.0 的标签：

```
git tag 1.0.0 1b2e1d63ff
```

1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。使用如下命令获取提交 ID：

```
git log
```
你也可以用该提交 ID 的少一些的前6位。

### 替换本地改动

```
git checkout -- <filename>
```
此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到缓存区的改动，以及新文件，都不受影响。

假如你想要丢弃你所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它：

```
git fetch origin
git reset --hard origin/master
```

### 有用的贴士

- 内建的图形化 git：gitk
- 彩色的 git 输出：git config color.ui true
- 显示历史记录时，只显示一行注释信息：git config format.pretty oneline
- 交互地添加文件至缓存区：git add -i

