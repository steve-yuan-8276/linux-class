---
title: 'Linux运维学习笔记3-1：用户配置文件和密码配置文件'
date: 2018-04-02 14:11:37
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

上周五晚上手贱把mac系统升级到最新的10.13.4，升级过程还算顺利，哪知道周日晚上再打开电脑系统就崩溃了——所有的文件、系统设置统统都没有了……重装系统后，发现有6个系统按键又失灵了！

这件事的直接后果是：

1. 所有的系统文件丢失，虚拟机神马的全都没有了；
2. 已预约mac直营店修

当然，这件事同时也说明了，**系统备份还是很有必要的！！！**

### linux 和 windows 文件互传

**注：**此方法适用于win 和 linux 之间，并且win上需要安装xshell

安装插件 `lrzsz`

![](https://farm1.staticflickr.com/804/27304023598_990a31814e_o.png)

<!--more-->

用法：

* linux to win： `sz [filename]`   弹出对话框选择文件

* win to linux：` rz [filename]`    弹出对话框选择文件

![](https://farm1.staticflickr.com/899/27304203068_b10fa7b9ca_o.png)

### 用户配置文件和密码配置文件

#### `/etc/passwd` 用户配置文件

使用命令： `cat /etc/passwd |  head`

![](https://farm1.staticflickr.com/898/39367736930_eefeb54ab9_o.png)

 如上图所示，共有7个字段，具体含义如下：
 
 字段 | 含义 | 备注
 ---|---|---
 1 | user name，用户名| 用户名可以由大小写字母、数字、减号、点或者下划线构成
 2 | password | 由于安全因素，这里的password 被放在`/etc/shadow` 当中，这里用`x`代替。
3 | uid | 用户标示号，root用户的uid是0；uid1000以内的系统预留，普通标示号从1000开始
4 | gid |  组标示号
5 | 注释说明 | 如姓名、电话、地址等，可以用 `chfn` 命令进行更改
6 | 用户家目录 | root 的家目录为 `/root `；普通用户的家目录为 `/home/username`
7 | 用户的shell | 常见的shell包括：`bash`、`zsh`、`ksh` 等。另外，该字段还有/sbin/nologin，表示不允许该账号登陆

#### `/etc/shadow` 密码配置文件

查看命令： `cat /etc/shadow | head -n 3`

![](https://farm1.staticflickr.com/798/41134248042_16c2ae1e96_o.png)

通过上述命令查询到了shadow 文件的前三行内容，每行共分9段，含义如下：

字段|含义
---|---
1|用户名，username
2|password，此处显示的是加密之后的密文;**注：centOS 7的密码是通过sha-512加密的**
3|上次更改密码的时间，此处的时间是以1970年1月1日为起点算传来的
4| 经过多少天密码才可以修改，默认是0 ，表示不受限制
5| 多少天后密码到期，默认设为99999（大概是273年？）
6| 密码到期前的警告期限，默认是7
7| 密码到期后的失效期限
8| 密码的生命周期，计算方法跟3一样
9| 保留字段

### 用户和用户组管理

#### 增加用户 `useradd [参数] [username]`

常用参数| 含义
---|---
-u | 自定义uid
-g | 添加到已存在的某个组，后面可以是gid，也可以是组名
-d | 自定义家目录
-M | M为大写，不建立家目录
-s | 自定义shell

##### 示例 ：

- 不带参数，创建与当前用户同组的用户

![](https://farm1.staticflickr.com/788/40466234314_84b3e50b7f_o.png)

- `g` 添加到已经存在的某个组；如果gid不存在，则会报错

![](https://farm1.staticflickr.com/788/40283156665_c83e45c4dd_o.png)

- `M`  不创建家目录

![](https://lh3.googleusercontent.com/-SFXBZgAVk2A/WsH2kSNfwXI/AAAAAAABiCg/hehyQO4Jx1w6jzdGXPL0JouASEmfeazGQCHMYCw/I/15226610064487.jpg)

####  删除用户 `userdel [参数] [username]`
 
 -r 参数的作用是，同时删除用户及用户家目录；如果省略，则只删除用户，保留 用户家目录。
 
 ![](https://farm1.staticflickr.com/818/41135050292_91ff9b3381_o.png)
 
 使用 `-r` 参数 
 
 ![](https://farm1.staticflickr.com/891/41179445931_07d6d91f51_o.png)
 
 
#### 针对组的操作 groupadd 和 groupdel

##### 添加组 `groupadd [-g gid] [groupname]`

`-g `  为可选参数，用来定义gid，可以不添加。

![](https://farm1.staticflickr.com/902/40282856345_f2cbf1d4ae_o.png)

##### 删除组 `groupdel [groupname]`

![](https://farm1.staticflickr.com/867/41134554112_d741a6044a_o.png)

注，如果改组中包含用户，就不可以直接删除，原理类似于 `rmdir `不能删除非空目录。





