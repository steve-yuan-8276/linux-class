---
title: 'Linux运维学习笔记5-3：shell基础知识（一）'
date: 2018-04-17 13:43:44
categories: linux
tags: [centos, linux, shell, notes] 
---

### 写在前面

这节课开始，整理shell基础知识，共分三节。第一节主要包括命令历史、别名、命令补全、通配符、输入输出重定向。

### 什么是`shell`？

shell 是一种命令解释器，也是一种脚本语言。shell编写的程序成为脚本（script），能够调用所有的UNIX系统命令、实用程序、工具软件，以及二进位制文件;支持循环、判断等特定语法。

CentOS 默认安装的shell，叫做bash（Bourne Again Shell），简称sh。

目前，常见的shell，还有zsh、ksh等。

### 命令历史 `history`

#### 查看命令历史

- `history` 命令可以查看命令历史

```
[root@stevey ~]# history | less
```

![](https://farm1.staticflickr.com/810/39704270190_2f4e734d8c_o.png)

- `history -c` 清空内存中的命令历史；

**注：**

1. 该操作并不会清空`.bash_history` 内容。

2. 命令历史只有在shell退出的时候，才会保存到`.bash_history` 当中。

<!--more-->

#### history配置文件及修改

##### history配置文件

bash 可以记录命令历史，保存在当前用户家目录下的`.bash_history` 当中，默认支持1000条，

```
[root@stevey ~]# head /root/.bash_history   //注：当前是root操作
head /root/.bash_history
vi .ssh
rm -rf .ssh
ls
mkdir -p .ssh
cd .ssh
touch authorized_keys
vi/root/.ssh/authorized_kesy
vi /root/.ssh/authorized_keys
cd ~
chmod 700 .ssh
```

##### 如何修改history配置文件？

第一步：编辑配置文件

```
vim /etc/profile
```

第二步：修改配置文件

```
HISTSIZE=n    //n为最大条数，默认1000

HISTORYFORMAT='%Y/%m/%d %H/%M/%S'    //记录命令时间
```

第三步：source

```
source  /etc/profile   
```

##### history永久保存

```
chmod +a ～/.bash_profile
```

**注：**该配置只对正常退出shell的时候才有效。

#### 与命令历史相关特殊用法

- `!!` 执行上一条命令

![](https://farm1.staticflickr.com/785/40620060345_49eee4cbaa_o.png)

- `!n` n代表数字，执行命令历史中的第n条命令

![](https://farm1.staticflickr.com/884/27642315268_57d16252b1_o.png)

- `!$` `$` 代表上一条命令最后的那个值

![](https://farm1.staticflickr.com/784/41513163881_e0fc41cbda_o.png)

- `![字符串]` 字符串最少要有一个字符，比如m表示命令历史中最近的一个以m开头的命令

![](https://farm1.staticflickr.com/904/40620109295_345891e1ab_o.png)

### 命令补全` tab`及其别名

#### 补全插件安装 tab

- shell的命令补全功能需要先安装插件

```
root@stevey php-7.2.4]# yum install -y bash-completion

init 6     //重启电脑，使用 reboot 也可以。
```
安装完成后重启电脑，就可以生效了。

#### alias 别名 

终端输入 `alias` 可以查看当前已经设置的别名

![](https://farm1.staticflickr.com/829/41471940632_7e193e44b4_o.png)

**注意：**

1. centos中，别名的配置文件在`～/.bashrc` ，也可以通过`vim ～/.bashrc`进行编辑设置；也有部分在 `/etc/profile.d/`下定义。

2. `alias` 在macos下同样有效，不同在于配置文件在`～/.bash_profile`，也可以通过 `vim ～/.bash_profile` 进行编辑设置。

##### 设置别名： `alias [别名]=[具体命令]`

别名是一个很有用的命令，可以玩出很多花样。比如，可以将平常的工作目录都是设置一个别名，这样就不用每次都输入一长串命令了。

**注：**

1. 具体命令用英文单引号括起来；

2. 括号两边是没有空格的；

```
[root@stevey ~]# alias test='cd /root/test/'
[root@stevey ~]# test
[root@stevey test]# pwd
/root/test
```

##### 取消别名： `unalias [别名]`

```
[root@stevey test]# unalias test
[root@stevey ~]# test
-bash: test: command not found
```

#### shell当中的特殊字符

##### `* `用来匹配**一个或多个**字符

![](https://farm1.staticflickr.com/842/40620352085_00c6400957_o.png)

##### `？`用来匹配**一个**字符。

![](https://farm1.staticflickr.com/940/40620364715_fdbdb6c01b_o.png)

##### [m-n]表示范围

注：m、n可以是数字范围，也可以是字母；符合范围中的一个，就可以被匹配出来；

![](https://farm1.staticflickr.com/824/39764895570_63a6ff4281_o.png)

##### {m,n} 表示范围中的一个，逗号是或的意思

m和n中间用逗号连接，是或的意思，符合范围中的**一个**可以被匹配出来。

```
[root@stevey test]# touch {1,3}.txt   //创建a.txt和3.txt
[root@stevey test]# ls
1.txt  3.txt  b.txt  test1  test2  test3
```

##### 注释符号`#`

跟js当中的 `//`， python中的` #` 一样，这里也是表示注释的意思，` #` 后面的内容会被忽略。

```
[root@stevey ~]# a=2 #sdscsdc      //#后面是注释
[root@stevey ~]# echo $a
2
```

##### 脱义字符 `\`

比如，`*` `？`在shell当中都是特殊字符，如果恰好就想输入`*`或者`？`本身，就需要使用到脱义字符；使用脱义字符后，后面跟的特殊字符会还原成普通字符。

```
[root@stevey ~]# ls *.txt    //这里*是通配符，表示一个或多个字符，下面输出结果都匹配这个规则
1.txt  a.txt  b.txt
[root@stevey ~]# ls \*.txt     //跟在脱义符后面，*就是普通字符，特指`*.txt`这一个文件。
ls: cannot access *.txt: No such file or directory
```

##### 管道符 `|` 

管道符的作用，**一般针对文档操作比较常用**，就是将前面命令的输出结果作为后面命令的输入。

常见的，比如：

* ` cat head tail less more`：这些都是查看文档的；

* `grep`：正则匹配工具；

* `sort`：排序；

* `wc`：统计，通常跟`-l` 选项；

* `cut`：截取字符

* `uniq`：检查及删除文本文件中重复出现的行列，常用参数 -c ，命令行格式：uniq-c testfile 检查文件并删除文件中重复出现的行，并在行首显示该行重复出现的次数。

* `tee`：读取标准输入的数据，并将其内容输出给文件，类似于输出重定向，不过会同时显示出来，经常跟在管道符后面。

* `tr`：替换或删除文件中的字符。比如，要将文本内容读取出来转换成大写字符，可以使用`cat testfile |tr a-z A-Z` 或者 `cat testfile |tr [:lower:] [:upper:] `，两者作用是一样的。

* `split`：将一个文件分割成若干个。常见用法，比如`split -n README` n 表示行数，把文件每n行为一组，拆分切割成多个以"x"开头的小文件。

* `sed`：利用script来处理、编辑文本文件。这个比较复杂，教程在[这里](http://www.runoob.com/linux/linux-comm-sed.html)。

* `awk`：同样很复杂，教程在[这里](http://www.runoob.com/linux/linux-comm-awk.html)。

#### 输入输出重定向

命令	|说明
---|---
command > file|	将输出重定向到 file。
command < file	|将输入重定向到 file。
command >> file|	将输出以追加的方式重定向到 file。

用法示例：

- 输出重定向 `command1 > file`

示例1:

```
[root@stevey ~]# touch 1.txt
[root@stevey ~]# who > 1.txt    //who命令用于显示系统中有哪些使用者，这里是将who命令的查询结果输出到1.txt
[root@stevey ~]# cat !$ 
cat 1.txt
root     tty1         2018-04-16 15:10
root     pts/0        2018-04-16 15:11 (172.16.155.1)
```

示例2：

```
[root@stevey ~]# echo 'this is a test' > 1.txt  //echo 命令用于字符串的输出
[root@stevey ~]# cat !$
cat 1.txt
this is a test
```

- 追加重定向 `command >> file`

```
[root@stevey ~]# pwd
/root
[root@stevey ~]# pwd >> 1.txt    //pwd查询结果追加到1.txt
[root@stevey ~]# tac 1.txt     //倒序查询1.txt内容，可以看到刚刚追加进去的内容

/root              
root     pts/0        2018-04-16 15:11 (172.16.155.1)
root     tty1         2018-04-16 15:10
```

- `2>` 错误重定向

```
[root@stevey ~]# ls aaa 2> a.txt
[root@stevey ~]# cat a.txt
ls: cannot access aaa: No such file or directory
```

- `2>>`  错误追加重定向

```
[root@stevey ~]# ls aaa 2>> a.txt
[root@stevey ~]# cat a.txt
ls: cannot access aaa: No such file or directory
ls: cannot access aaa: No such file or directory
```

- `&>` 重定向 相当于上述输出重定向和错误重定向的集大成者

![](https://farm1.staticflickr.com/792/41574153231_c16621fe01_o.png)

- 输入重定向 `command < file`  (不常用，仅作了解即可)

```
[root@stevey test]# cat 1.txt
czsdfvf
ewfefw
raferfw2
egregrere
gerer23fss
[root@stevey test]# wc -l < 1.txt
5     //输入重定向
[root@stevey test]# wc -l 1.txt
5 1.txt

```




