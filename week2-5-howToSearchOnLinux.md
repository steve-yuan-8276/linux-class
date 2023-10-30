---
title:  'Linux运维学习笔记2-5：如何在linux下进行文件搜素'
date: 2018-03-28 21:19:15
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

这则笔记简要总结文件类型，以及如何在linux系统进行文件搜索。

### linux系统文件类型与后缀名

#### linux文件类型

在Linux当中，一切设备均为文件。使用` ll  [filename]` 或者 `ls -l [filename/dirname]`  查看文件类型。在[这则笔记](https://www.steve-yuan.com/2018/03/15/week1-5-centOS-filesystem-ls-aliasCommand/#more) 中已经进行总结。这里再整理一下常用类型用法：

##### 普通文件：

一般类型的文件，主要包括：

1. 纯文本档`ASCII`：文件内容可以直接读到数据，例如：字母，数字等。这里文件可以用` cat` 命令进行查看。

2. 二进制文件`binary`：Linux当中可执行文件就是属于这种格式。例如，ls、 rm、mkdir 等等常见命令。

3. 数据文件`data`：有些程序在运行的过程中会读取某些特定格式的文件，那些特定格式的文件可以被称为数据文件。例如，系统登录数据会记录在` /var/log/wtmp` ，这就是一个数据文件，可以用`last -f /var/log/wtmp` 命令查看。

<!--more-->

![](https://farm1.staticflickr.com/805/39264033010_f44a3df2ca_o.png)

##### 目录 `directory` ：

 跟`windows` 下的文件夹类似，用 `ll` 命令查看属性的第一个字母为`d`

##### 链接文件 `link file `

类似`Windows`系统底下的快捷方式！ 第一个属性为` l `，分为软链接和硬链接，这个上一则笔记已经整理过了，看[这里]()。

##### 设备文件 `device`

1. 区块设备文件 `block`：就是一些储存数据， 以提供系统随机存取的接口设备，第一个属性为 `b` 。例如，硬盘、软盘等。

2. 字符设备文件 `character`：串行端口的接口设备，第一个属性为` c` 。例如，键盘、鼠标等。


#### Linux 常用文件后缀名

**注意：**与Windows系统不同，在Linux系统下， 一个文件是否能够被执行，关键是看有没有x 权限，与文件的后缀名无关。

这里的后缀名，仅仅是为了让我们便于区分，习惯在定义文件名是加上一个后缀名。

##### 常见的文件后缀名包括：

* `.sh` ： shell脚本或批处理文件 (scripts)。

* `.tar`, `.tar.gz`, .`zip`, `.tgz`： 经过打包的压缩文件。之所以有这么多后缀名，是对应不同的压缩软件，比如gunzip, tar 等。

* `.conf`：系统的配置文件。

* `.pl`：perl语言文件，通过perl语言开发的程序。

* `.html`，`.htm`，`.php`，`.jsp`：网页文件。

* `.py`：Python语言文件，通过Python语言开发的程序。

* `.rpm`：rpm安装的文件。

### linux如何查询文件

#### find 搜索文件

**注：find是常用命令，需要熟练掌握。**

命令行格式： `find [路径] [参数] -print`

上述命令可以简单地理解为` find + 限定条件` 通过限定条件来查找所需的文件。`-print `将匹配的文件输出到标准输出，可省略。

##### 结合路径进行搜索

路径，此选项可省略，指的是查找的目录路径，比如要在`home`目录下找某个文件，就是`/home` ；需要在`etc` 目录下找某个配置文件，就是 `/etc`。

###### 查找$HOME目录中的文件 

```
find ~ -name "*" -print   //这两者写法都可以
find . -print  
```

###### 在当前目录及子目录中查找所有的‘ *.log‘文件

```
find . -name "*.log" -print  
```

###### 在当前目录及子目录中查找文件名以一个大写字母开头的文件  

```
find . -name "[A-Z]*" -print  
```

##### 结合参数进行搜索

###### `-name `  按照文件名查找文件

```
find . -name [filename]
```

###### `-type `按照文件类型查找文件

```
find . -type [filename]
```

* b - 块设备文件。 
* d - 目录。 
* c - 字符设备文件。 
* p - 管道文件。 
* l - 符号链接文件。 
* f - 普通文件。 

###### `-time -n/+n` 按照文件的更改时间来查找文件

文件的inode当中，有三个时间time 信息，分别是：

* atime：Access time，在读取文件或者执行文件时更改，即文件最后一次被读取的时间。查看atime，使用 `ls -lu filename`

* mtime：Modified time，在写入文件时随文件内容的更改而更改，是指文件内容最后一次被修改的时间。查看atime，使用` ls -l  filename`

* ctime：Change time，在写入文件、更改所有者、权限或链接设置时随 Inode 的内容更改而更改，即文件状态最后一次被改变的时间。查看atime，使用 `ls -lc filename`

后面的 n 表示天数，比如 -atime +n/-n 表示访问或者执行时间大于或小于n天的条件。

###### `-perm` 按照文件权限来查找文件

``` 
find . -perm 755 –print    //在当前目录下查找文件权限位为755的文件
```

###### `-user` 按照文件属主来查找文件

```
find ~ -user sam –print     //在$HOME目录中查找文件属主为sam的文件
```

模糊搜索


更多find 参数，可以在这两个文章查询：

* [inux中强大且常用命令：find、grep](https://www.cnblogs.com/skynet/archive/2010/12/25/1916873.html)

* [每天一个linux命令（22）：find 命令的参数详解](https://www.cnblogs.com/peida/archive/2012/11/16/2773289.html)

#### which 查询可执行文件的绝对路径

命令行格式： `which [filename]`

![](https://farm1.staticflickr.com/799/41046981492_381002a7b0_o.png)

#### whereis 查找文件

命令行格式： `whereis [参数] [filename]`

参数| 含义
---|---
-b | 只查找二进位制文件
-m | 只查找帮助文件（`man` 目录下的文件）
-s | 只查找代码文件

![](https://farm1.staticflickr.com/877/40380947864_168e70a3a8_o.png)

#### locate 查找文件

跟whereis 作用类似，通过查找预先生产的文件列表库来查找文件。

`locate `命令 需要单独安装，

```
yum -y install mlocate
```

安装完成后，初次使用需要生产文件列表库

```
updatedb
```

查找文件

```
locate [filename] 
```

**注：**默认情况下，数据库每周更新一次；如果一个文件正在两次更新时间段之间的创建的，就搜不到结果。

### 参考资料：

* [每天一个linux命令(24)：Linux文件类型与扩展名](https://www.cnblogs.com/peida/archive/2012/11/22/2781912.html)

* [Linux文件类型与扩展名](https://www.jianshu.com/p/eda80af7a185)

* [Linux文件类型及文件扩展名](http://labs.supinfochina.com/linux%E6%96%87%E4%BB%B6%E7%B1%BB%E5%9E%8B%E5%8F%8A%E6%96%87%E4%BB%B6%E6%89%A9%E5%B1%95%E5%90%8D/)

* [查看 /var/log/wtmp文件的方法](http://blog.51cto.com/wuhaoshu/397767)

* [Linux 文件时间详解 ctime mtime atime](https://blog.csdn.net/doiido/article/details/43792561)

* [inux中强大且常用命令：find、grep](https://www.cnblogs.com/skynet/archive/2010/12/25/1916873.html)

* [每天一个linux命令（22）：find 命令的参数详解](https://www.cnblogs.com/peida/archive/2012/11/16/2773289.html)




