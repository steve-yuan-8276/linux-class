---
title: 'Linux运维学习笔记1-5：文件目录、类型及ls、alias基本操作'
date: 2018-03-15 17:27:53
categories: linux
tags: [centos, linux, notes] 
---

### centos系统目录结构

目录名称| 解释|备注
---|---|---
/home | 用户目录|每个用户都有一个`/home` 目录，一般是以账户名命名。
/bin, /usr/bin |存储系统用户使用的命令|如 `ls`
/sbin,  /usr/sbin| 存储root用户才能使用的命令|如 `ifconfig`
/boot|引导加载程序文件| 内核的`initrd`、`vmlinux`、`grub`文件位于/boot下。
/dev|linux系统中特有的设备文件|例如： 光盘，鼠标，硬盘等连接到系统的设备。
/etc|系统配置文件|通常编辑的配置文件都在此目录下。
/lib和/lib64|存放系统的库文件|作用类似于windows中的DLL文件。
/media|媒介目录，通常为空|插入U盘、移动硬盘，默认挂载在/media下
/mnt|临时挂载的目录，通常为空|可以挂载光驱到该目录之后，进入目录查看内容
/var|存放日志的地方|例：`/var/message`   默认存放系统日志的地方

<!--more-->

更详细的看这里
![](https://farm1.staticflickr.com/787/40206810334_bb89977f05_o.jpg)
[来源：网络]

### 文件类型

输入 `ls -l  <filename>` 查看文件详细信息，如下图所示：

![](https://farm1.staticflickr.com/808/39996821785_cc07ae8a8b_o.png)

红圈处的11位信息（包括末尾的` . `），表示文件的类型、所有者、所属组及其他用户的权限。

#### 常见文件类型

文件类型| 描述
---|---
d  | 文件目录。
l  |链接文件(指向另一个文件,类似于windows下的快捷方式)。
s  |套接字文件，用于进程通信
b  |块设备文件,二进制文件。
c  |字符设备文件，如鼠标、键盘、终端
p  |命名管道文件。
-  |普通文件

### 常用的几个命令

#### 安装tree 命令查看目录结构

tree命令是以树状形式显示目录下的文件或子目录；系统默认没有的，需要自行安装。

##### 安装

```
yum install -y tree
```

##### 使用

```
tree -L 2     //-L 表示查看目录层级，2表示查看两级目录。
```

ls 查看目录信息


#### ls 命令

名称 | 作用
---|---
ls| 列出当前目录下的文件和文件夹，不包括隐藏文件
ls -a| 列出当前目录下的文件和文件夹，不包括隐藏文件
ls -l | 列出除隐藏文件之外的所有文件和目录的详细信息，包括权限、所属主、所属组、文件创建日期时间
ls `<filename>` -l | 查看某个文件的详细信息
ls `<file-Folder-name>` | 查看指定目录下的文件和目录
ls -Rl | 显示当前目录及所有子目录信息
ls -tl |以时间排序显示目录,查找最新文件有用
ls -Sl | 以文件大小排序
ls -s -l -S |显示文件大小,并按大小排序


#### alias 命令

alias 命令用来设置指令别名，如果命令过长，那么就考虑可以设置一个别名。

##### 基本使用方法：

```
alias 新的命令='原命令 -选项/参数'
```

显示当前已设置的别名

```
alias
```

![](https://lh3.googleusercontent.com/-wCFJNyclSxs/Wq8hgnOzutI/AAAAAAABhlw/v12kCqp-610mvOQAqHoxq52xiHSaKzSlACHMYCw/I/15214268144359.jpg)

设置vim别名

```
// 设置别名，输入`vi` 调用 `vim`
alias vi='/usr/bin/vim'
```

删除别名

```
unalias vi
```

查询` alias` 更多用法

```
man alias  //按 `space `下翻页，按` q `退出。
```


#### 更多资料

* [Linux文件系统目录结构](https://monkeysayhi.github.io/2018/02/10/Linux%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84/)

* [深入理解linux系统下proc文件系统内容](https://www.cnblogs.com/cute/archive/2011/04/20/2022280.html)

* [Linux根目录下各子目录的作用](https://github.com/Entiy/bravo/wiki/Linux%E6%A0%B9%E7%9B%AE%E5%BD%95%E4%B8%8B%E5%90%84%E5%AD%90%E7%9B%AE%E5%BD%95%E7%9A%84%E4%BD%9C%E7%94%A8)


