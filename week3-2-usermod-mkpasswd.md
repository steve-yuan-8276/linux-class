---
title: 'Linux运维学习笔记3-2：用户密码管理'
date: 2018-04-03 14:42:36
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

这则笔记主要记得是用户密码管理相关的知识。

### usermod 更改用户属性

语法：`usermod [参数] [filename]`

常用参数 | 解释
---|--- 
-c|<备注>：修改用户帐号的备注文字；
-d|<登入目录>：修改用户登入时的目录；
-e|<有效期限>：修改帐号的有效期限；
-f|<缓冲天数>：修改在密码过期后多少天即关闭该帐号；
-l|修改用户帐号名称；
-s|修改用户登入后所使用的shell；
-u|uid，修改用户ID；
-g|<群组>：修改用户所属的群组；
-G|<群组>；修改用户所属的附加群组；
-L|锁定用户密码，使密码无效；
-U|解除密码锁定。

<!--more-->

举例：

```
usermod -u 1008 user1     //更改uid

usermod -g 10098 grp2     //更改gid

usermod -d /home/user123 user4  //更改user4的家目录

usermod -G grp1 grp2  //修改附加组

usermod -g  groupname  username    //更改用户组
```

注：使用` -g` 只能修改用户所属的一个组；使用` -G `能够添加到若干个附加组。

#### usermod 特殊用法：通过 `-s` 禁止用户登陆

命令行 `useermod -s [pathToShell] [UserName]` 可以修改用户登入后使用的shell，也就是`/etc/passwd`第7列第内容，因此也可以利用 `/sbin/nologin`和`/bin/false`的特性，实现禁止用户登陆的目的。

`/sbin/nologin` ：不允许系统login，可以使用ftp等其他服务；

`/bin/false` ：最严格的禁止login选项，一切服务都不能用，

示例

```
usermod -s /sbin/nologin user1 //不允许user1用户登陆，但可以使用ftp；

usermod -s /bin/false user1 //禁止user1 用户登陆，神马都不能作。
```

### 用户密码管理

#### `passwd` 设置用户密码

语法： `passwd [username]`

##### 密码设置的基本原则：

* 长度不小于10个字符；
* 密码包括大小写字母、数字、特殊字符（如*、&、%）等；
* 不规则（不要用常用单词或者数字）；
* 不使用带有自己的名字、电话、生日及公司名称等。

注：
1. root 用户使用此命令，不加 username 表示更改root 密码；加上username 表示修改该指定用户的密码。
2. 普通用户只能修改自己的密码。

##### `passwd `的特殊用法

- `/etc/shadow` 中第二列表示密码，此处为`!!` 表示密码为空；此处为`*` 表示密码锁定。这两种情况下，账户都是不允许登陆。

![](https://ws2.sinaimg.cn/large/006tKfTcly1fpzihczqqpj30iy0aa123.jpg)

- 锁定和解锁账户

```
passwd -l username //锁定用户
passwd -u username  //解锁用户
```

- 使用`usermode`命令也可以实现同样的效果

```
usermod -L username  //锁定用户
passwd -U username   //解锁用户
```

- 设定密码 `passwd --stdin username`

![](https://ws1.sinaimg.cn/large/006tKfTcly1fpzitd923aj30sk07q48u.jpg)

注：此处密码只需要输入一次，而且是明文显示的。

- 使用管道符，通过一行命令更改用户密码

![](https://ws4.sinaimg.cn/large/006tKfTcly1fpzixbygvaj30qy0580z7.jpg)

- 使用参数`-e` ，通过一行命令更改密码,  例如`echo -e 'abc123456\nabc123456' | passwd user1`

![](https://ws4.sinaimg.cn/large/006tKfTcly1fpzjfp4o9gj30s8088dj8.jpg)

#### mkpasswd 命令

linux系统默认没有此命令，需要单独安装 `yum -y install expect `

基本用法：`mkpasswd`

![](https://ws3.sinaimg.cn/large/006tKfTcly1fpzhv614h1j30jk044n0t.jpg)

生成指定长度的密码 `passwd -l n` n代表数，即长度为多少位

![](https://ws2.sinaimg.cn/large/006tKfTcly1fpzhxu56s8j30im03w0w9.jpg)
  
指定特殊字符和数字 `passwd -l n -s n -d n` 

* `l`表示密码长度
* `s`表示特殊字符
* `d`表示数字

![](https://ws1.sinaimg.cn/large/006tKfTcly1fpzi3qvycnj30o6044q7p.jpg)

### 参考资料

* [usermod命令](http://man.linuxde.net/usermod)

* [passwd命令](http://man.linuxde.net/passwd)


