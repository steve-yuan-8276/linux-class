---
title: 'Linux运维学习笔记3-3：用户身份切换'
date: 2018-04-03 16:39:26
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

这则笔记主要整理用户身份切换方面的知识，涉及su 和 sudo 两条命令。

#### 查询当前用户 `whoami`

![](https://ws3.sinaimg.cn/large/006tKfTcly1fpzkhqafpgj30h806wafi.jpg)

**注：**root 用户前面是 `#` ；切换到普通用户后，前面是 `$`.

处于安全考虑，建议使用普通用户，而不是`root`用户进行日常管理，需要的时候再使用`su` 或者 `sudo` 临时使用管理员权限。

<!--more-->

### su命令：切换用户

语法： `su [-] username`

- 不使用` - `，切换root，仍然在当前家目录，环境变量并不改变

![](https://farm1.staticflickr.com/877/41204926171_0c46590788_o.png)

- 使用 `- `切换 root，同时切换环境变量

![](https://farm1.staticflickr.com/874/40308901895_68ba7fbcbb_o.png)

注：从root用户切换到普通用户，不需要输入密码；反之，普通用户切换到root用户则需要输入密码。

- 临时切换用户运行一条命令 `su - -c [command] [username]`

![](https://farm1.staticflickr.com/889/27350659908_9100edecf5_o.png)

### sudo 命令

使用`su`命令仍然有密码泄露的风险，因此`sudo` 的用武之地。使用 `sudo [command]` ，可以让普通用户执行只有 `root` 用户有权执行的命令。

使用`sudo` 命令需输入当前用户的密码，而不是系统管理员`root`的密码，这样就降低root密码扩散是风险，有助于提升系统用户的安全性。 

`sudo -i`  为了频繁使用root用户权限使用的命令，该命令的特性包括：

* 提示输入密码时该密码为当前账户的密码；
* 密码只用输入一次；
* 没有时间限制。
* 执行该命令后提示符变为`#`而不是`$`；
* 退回普通账户时可以执行`exit`或`logout` ；

### `su` 和 `sudo`的进阶玩法

####  简单认识`/etc/sudoers`

输入 `visudo` 对`/etc/sudoers`进行编辑，关键是下面这一行：

![](https://farm1.staticflickr.com/868/40629342644_6353c0544e_o.png)

root 表示这是root用户，下面的几个all的意思是：

* 第一个ALL，表示"From ALL hosts"（所有主机）, 意思是 `root` 从任何机器登录，都可以应用接下来的规则。

* 第二个ALL，表示“run as All user”，意思是`root`可以以任何用户的身份运行一些命令。

* 第三个ALL，表示“run All commands”，意思是`root`可以以任何用户组的身份运行任何命令。

上面几个ALL合起来的意思是，`root`这个用户可以从任何机器登录，以任何用户和用户组的身份运行任何命令。

#### 如何编辑`/etc/sudoers`

配置`/etc/sudoers` 需要使用`visudo` 进行编辑，好处是会自动校验配置文件，会自动检验配置文件改动语法是否有错误，使用起来也就更安全。

##### `visudo`安装

```
yum install -y sudo
```

##### `visudo`基本使用

```
visudo   //打开编辑`/etc/sudoers`

```

基本使用根vi/vim差不多，`h、l、k、j`进行方向选择；`x`删除单个字符，`dd`删除整行；`/` 向后搜索，`？`向前搜索；按`i` 进入编辑模式。

保存退出：按esc ，输入 :wq ; 如果出现错误，在保存退出的时候会有提示
*  `e `返回进行修改，确认没有问题后再正常保存退出；
*  w! 强制保存（**严重不推荐**）；

#### visudo配置的两个案例


##### 案例一：配置`/etc/sudoers`，让普通用户能够拥有root 权限,同时可以执行ls、mv、cat命令。

###### 方法一：

修改用户权限配置

![](https://farm1.staticflickr.com/785/39547231440_415cdaac60_o.jpg)

**注：** /usr/bin/ls, /usr/bin/mv, /usr/bin/cat    需要修改成绝对路径才能够正常使用。

保存退出，提示93行有错误，原来是刚才的路径写错了，应该从 `/` 根目录写起，修改后重新保存。

![](https://farm1.staticflickr.com/808/39534292230_1e3400aee3_o.png)

检验一下工作成果，初次使用会有风险提示，但是看起来很拽有木有！输入密码后可以拥有`super power` 了。如下图：

![](https://farm1.staticflickr.com/868/27471558718_3ccd10dac6_o.png)

###### 方法二：

设置命令别名

![](https://farm1.staticflickr.com/875/26472521627_e9b3169405_o.png)

再修改账户权限
![](https://farm1.staticflickr.com/900/27471895258_f7deaca3dc_o.png)

保存退出，结果也是一样的。


##### 案例二： 配置`/etc/sudoers` ，使得普通用户不用户输入密码就可以使用`sudo`

为了满足在防止root密码泄漏的同时，最大限度满足系统用户需要以root身份运行的需求，可以采取以下策略，使得用户可以以普通身份登陆，之后再免密切换到root用户。

基本思路，就是要在配置文件中加入以下几行内容：

```
USER_Alias_USER_SU = yuanfeng, user1   //设置用户alias
Cmnd_Alias_SU = /usr/bin/su     //设置权限alias
USERS_SU ALL = (ALL) NOPASSWD: SU     //设置免密su
```

创建一个user alias
![](https://farm1.staticflickr.com/813/41313040822_bc8252888d_o.png)

设置一个规则免密执行su命令登录root
![](https://farm1.staticflickr.com/881/27485229258_96495bca13_o.png)

检验一下学习成果，普通用户输入 `sudo su - `，免密切换到root用户。

![](https://farm1.staticflickr.com/800/40460996875_30204e291d_o.png)

最后，还可以禁止root用户远程登录，实现步骤：编辑/etc/ssh/sshd_config,将 `PermitRootLogin yes` 中的` yes `改为` no`，保存退出后，重启`sshd`服务。

```
systemctl restart sshd.service
```

通过上述步骤，就可以实现了我们的目的，齐活。

### 扩展阅读

[sudo与su比较](http://www.apelearn.com/bbs/thread-7467-1-1.html) 

[sudo配置文件样例](www.opensource.apple.com/source/sudo/sudo-16/sudo/sample.sudoers)

[sudo -i 也可以登录到root吗？](http://www.apelearn.com/bbs/thread-6899-1-1.html)

### 参考资料

[Sudo的用法和Visudo设置](https://www.jianshu.com/p/009e748db9e8)

