---
title: 'Linux运维学习笔记5-4：shell基础知识（二）'
date: 2018-04-17 15:53:39
categories: linux
tags: [centos, linux, shell, notes] 
---

### 写在前面

这则笔记接着整理shell基础知识，主要涉及管道符、作业控制，以及环境变量方面的知识。

### 管道符和作业控制

#### `|` 管道符用于将前一条指令的输出作为后一条指令的输入

示例：

- `wc`命令用来统计指定文件中的字节数、字数、行数，并将统计结果显示输出。常见参数`-c`表示字节数；`-l`表示行数。与`wc`连用，可以实现统计的效果。

```
[root@stevey ~]# cat /etc/passwd | wc -l  //统计passwd一共有多少行
21

[root@stevey ~]# ll | wc -l  //统计当前目录下一共有多少文件
12
```

- `grep `命令是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

![](https://farm1.staticflickr.com/792/27644203498_9ae6d37d78_o.png)

<!--more-->

#### 作业控制

指的是在shell运行当中的过程控制，主要包括：

* 暂停：`ctrl + Z`

* 前台恢复：`fg`  （指的是foreground）

* 后台运行：`bg`  （指的是background）

* 撤销：`ctrl + c`   （这个最常用，尤其是输错命令的时候）

练习环节：

实验了一下教材练习，有几个需要注意的点：

- 当有两个以上的任务都被暂停的时候，bash 会给暂停的任务编号。此时，输入 jobs 可查看；切换任务使用 `fg n`，其中 `n` 为任务编号。

![](https://farm1.staticflickr.com/800/27644458708_9e2d6ae771_o.png)

使用`& `，命令在后台运行，就会显示该任务的`pid`，如果要结束任务，使`用kill + pid` 

```
[root@stevey ~]# vmstat 1 > a.txt &
[3] 76388
[root@stevey ~]# kill 76388   //结束该任务
[root@stevey ~]# ps    //ps命令查询当前进程
   PID TTY          TIME CMD
   993 pts/0    00:00:00 bash
 76367 pts/0    00:00:00 vi
 76368 pts/0    00:00:00 vmstat
 76389 pts/0    00:00:00 ps
[3]   Terminated              vmstat 1 > a.txt      //这就是刚才被结束的进程
```

### shell变量

变量，可以理解为通过一些比较简单的字符来替代某些具有特殊意义的设定或者数据。比如，因为有了PATH变量，查看目录的时候，只需要`ls`即可。

#### 系统环境变量都存在哪里？

##### 系统级配置文件：

**注：**对系统级配置的更改会影响到所有用户；

文件名|特性|包含内容
---|---|---
`/etc/profile` |login shell| 该文件预置了PATH、USER、LOGNAME、MAIL、INPUTRC、HOSTNAME、HISTSIZE、umask
`/etc/bashrc` | no-login shell|该文件预设umask，以及PS1；PS1的格式为`[user@hostname dir]#` user是用户名；hostname是主机名；dir是当前目录；$是一个特殊字符，root用户此处是# 

###### `/etc/profile` 与 `/etc/bashrc`的区别

* `/etc/profile` 是`login shell`，通过终端输入用户名和密码进入到terminal，此时进入的shell环境就是`login shell`，例如，通过ssh远程进入到主机。因此，**通过login shell运行的shell命令放入到`.bash_profile`中。**

* `/etc/bashrc`是`no-login shell`，不需要输入用户名密码而进入的shell环境，通过gnome,KDE等桌面环境进入终端，此时进入的shell环境就是`no-login shell`。因此，**通过no-login shell运行的shell命令放入到`.bashrc`文件中。**

* Mac OS系统中，每次运行terminal，系统会默认运行一个login shell环境，因此mac系统配置都在`/etc/profile`和`.bash_profile`这两个文件中，一般安全起见，修改当前目录下的`.bash_profile`就可以了。

**延伸阅读：**

* [`.bashrc`和`.bash_profile`之间的不同](https://segmentfault.com/a/1190000004413842)

* [关于“.bash_profile”和“.bashrc”区别的总结](https://blog.csdn.net/sch0120/article/details/70256318)

##### 用户级别配置文件：

**注：**

1. 用户级别的配置文件都是隐藏文件，以 `.`  开头，如果没有，可以新建；

2. 对这里的配置文件进行修改，只会影响当前用户。

文件名|包含内容
---|---
`.bash_profile` | 定义当前用户个人华路径及环境变量的文件名称，当用户登陆时，该文件仅执行一次
`.bashrc`| 专属用户的shell的bash信息，可将别名、自定义变量写在这里，当登陆或每次打开新的shell，该文件都会被读取
`.bash_history`|记录命令历史
`.bash_logout`|定义用户退出时，需要执行的动作；当退出shell时，该文件会被执行

#### 如何查看变量？

##### `env` 显示及设置用户变量

![](https://farm1.staticflickr.com/896/26646203017_71006971e2_o.png)

变量名 | 含义
---|---
HOSTNAME | 主机名称
SHELL | 当前的shell
HISTSIZE | 执行命令的历史记录数量，默认是1000条
PATH | 表示shell将到那些目录查找命令或程序
PWD | 当前目录
LANG | 与语言相关的环境变量，中文为`zh_CN.UTF-8` ;english为 `en_US.UTF-8`
HOME | 当前用户家目录
LOGNAME | 当前用户登录名

##### `set`命令 :显示及设置shell变量

 包括的私有变量以及用户变量，不同类的shell有不同的私有变量 `bash`,`ksh`,`csh`每中shell私有变量都不一样。

![](https://farm1.staticflickr.com/841/41474873582_2fb48e1075_o.png)


###### 关于`PS1`的知识

`PS` ，即命令提示符，也就是终端最左侧用方括号括起来的部分；

PS1 是当前用户的命令提示符，是在用户根目录下的.bash_profile中定义的。

```
[root@stevey ~]# echo $PS1  //查看PS1
[\u@\h \W]\$   //含义为 当前用户名@不含域名的主机名 工作目录的最后一层目录名
```

默认特殊符号代表的意义

特殊符号| 含义
---|---
`\d` |代表日期，格式为weekday month date，例如："Mon Aug 1" 
`\H` |完整的主机名称。例如：我的机器名称为：fc4.linux，则这个名称就是fc4.linux 
`\h` |仅取主机的第一个名字，如上例，则为fc4，.linux则被省略 
`\t` |显示时间为24小时格式，如：HH：MM：SS 
`\T` |显示时间为12小时格式 
`\A` |显示时间为24小时格式：HH：MM 
`\u` |当前用户的账号名称 
`\v` |BASH的版本信息 
`\w` |小写w，完整的工作目录名称。家目录会以 ~代替 
`\W` |大写W，利用basename取得工作目录名称，所以只会列出最后一个目录 
`\#` |下达的第几个命令 
`\$` |提示字符，如果是root时，提示符为：# ，普通用户则为：$

在PS1中设置字符颜色的格式为`[\e[F;Bm]`，其中`F` 为字体颜色，编号为30-37；`B`为背景颜色，编号为40-47，具体如下：

`F`字体颜色| `B`背景颜色 |颜色
---|---|---
30| 40| 黑色
31| 41| 红色
32| 42| 绿色
33| 43| 黄色
34| 44| 蓝色
35| 45| 紫红色
36| 46| 青蓝色
37| 47| 白色

**自定义命令提示符**

方法一：使用export命令

```
PS1='\[\e[37;40m\][\[\e[34;40m\]\u\[\e[37;40m\]@\h \[\e[36;40m\]\w\[\e[0m\]]\\$ '
```

方法二： 修改 `/etc/bashrc`

```
vim /etc/bashrc

修改PS设定，按 `:wq` 保存退出。

source /etc/bashrc
```

方法三：自定义`custom.sh`

```
cd /etc/profile.d   //进入环境配置文件目录

touch custom.sh     //新建custom.sh,将PS1设定写进该文件并保存退出

source custom.sh

```
##### `echo $[变量名]` 查看某个变量的值

```
[root@stevey ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
[root@stevey ~]# echo $HOSTNAME
stevey
```

#### 关于变量设置的规则

命令行格式：`export A='Alibaba'`  其中，A代表变量名，`'Alibaba'`代表变量内容。设置变量要符合以下规则：

* 变量格式 a=b ,  其中a为变量名，b为变量内容，等号两边不允许有空格；

* 变量名只能由英、数字以及下划线组成，而且不能以数字开头；

* 当变量内容带有特殊字符（如空格）时，需要加上单引号；

* 如果变量内容中需要用到其他命令运行结果则可以使用反引号；

* 当变量内容含有特殊字符，需要做特殊处理，具体见下表：

变量内容 | 符号 | 示例
---|---|---
特殊字符（以空格）|使用单引号| myname='steve yuan'
变量内容本身有单引号|使用双引号|mac="steve'mac"
变量内容需要用到其他命令|使用反引号（tab键上面的那个键）|hotkey='pwd'
变量累加其他变量的内容|使用双引号|myname="$LOGNAME"Steve


#### 如何自定义变量

##### 方法一：修改全局变量  `/etc/profile`

```
vim /etc/profile     //编辑此配置文件，添加变量，修改完成后按`esc` 键，输入  `:wq` 保存退出。

source /etc/profile     //此方法会影响全局所有用户
```

##### 方法二：修改当前用户变量 `～/.bashrc`

```
vim ～/.bashrc     //编辑此配置文件，添加变量，修改完成后按`esc` 键，输入  `:wq` 保存退出。

source ～/.bashrc     //此方法子对当前用户有效
```

##### 方法三：`export [变量]` 更改全局变量

命令行格式： `export [参数][变量名称]=[变量设置值]`

参数| 描述
---|---
`-f` 　|代表[变量名称]中为函数名称。
`-n` 　|删除指定的变量。变量实际上并未删除，只是不会输出到后续指令的执行环境中。
`-p` 　|列出所有的shell赋予程序的环境变量。

- 列出当前所有的环境变量 `export -p`

![](https://farm1.staticflickr.com/888/39834264590_5dc723eb62_o.png)

- 定义环境变量赋值 `export [变量名称]=[变量设置值]`

![](https://farm1.staticflickr.com/828/27773365728_2236e477bd_o.png)

#### 取消变量 `unset [变量名]`

![](https://farm1.staticflickr.com/893/41642032071_e4c1cdde68_o.png)


