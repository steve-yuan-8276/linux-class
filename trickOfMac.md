---
title: 'mac笔记：关于mac的一些骚操作'
date: 2018-04-09 13:19:26
categories: mac
tags: [mac, bash, notes] 
---

### 写在前面

这则笔记主要整理一些在网上看到的关于mac的使用技巧。

### `Mac OS` 添加 `alias`

#### 解决思路：

修改系统配置文件`/etc/profile` 或者 `~/.bash_profile`，这两个文件的区别在于：

* `/etc/profile` 适用于所有用户的全局配置脚本

* `~/.bash_profile` 适用于当前用户的启动配置文件

所以，修改`~/.bash_profile`比较安全点。

<!--more-->

#### 实现步骤：

##### 修改`~/.bash_profile`

```
vim ~/.bash_profile   //使用vim编辑

alias ll='ls -l --color=auto'    //添加这一行，使得` ll `命令生效

alias subl="'/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl'"   // 使用 subl 打开sublime text

alias ping='ping -c 4'     //ping 命令默认执行4次

```

之后，按esc， 输入 :wq 保存退出；

##### 执行以下命令，配置文件生效

```
source ~/.bath_profile 
```

### 终端安装 mac-vim

#### 安装brew

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

```

#### 安装VIM

```
brew install vim --with-lua --with-override-system-vi

```

#### 安装 GUI 版 mac vim

```
brew install macvim --with-lua --with-override-system-vim
```

安装完成后重启终端即可。

#### 更新mac vim

```
brew upgrade macvim
```

输入 `mvim` 可以从终端启动 GUI 版的 Vim。


### 终端自动补全

#### 基本思路

修改`～/.inputrc`配置文件（如果家目录下没有就新建一个）；配置完成后，按tab键，就能够自动补全。

#### 实现步骤

- 第一步：

```
vim ~/.inputrc    打开编辑文件，加入一下内容

set completion-ignore-case on
set show-all-if-ambiguous on
TAB: menu-complete
```

- 第二步：完成后，按 `esc` ，输入 `:wq` 保存退出。

- 第三步：重启shell

