---
title: 'Linux运维学习笔记2-1：文档路径及基本操作'
date: 2018-03-19 16:00:22
categories: linux
tags: [centos, linux,  notes] 
---

### 相对路径和绝对路径

顾名思义，文件路径就是用来描述存放文件的地方。这里有两种描述的方法：

* 绝对路径：从根目录写起的，叫做绝对路径。
* 相对路径：与当前目录相对的写法，叫做相对路径。

举例来说：

pwd        //查询当前所在的绝对路径

![](https://farm1.staticflickr.com/808/27024710608_759c7aebd7_o.png)

上图中，红框标示出来的部分，就是当前所在的绝对路径，即 `/Users/stevey/documents/github-file` ，通过 ls 命令可以看到，下面有一个 steveBlog 的文件夹。

<!--more-->

要进入这个文件夹可以采用两种方法：

```
cd steveBlog        //相对路径

cd /Users/stevey/documents/github-file/steveBlog        //绝对路径
```

### 常见的文档操作命令

#### pwd 命令

用于显示当前所在目录。在linux系统中， `. `表示当前目录，  `.. `表示上一级目录。

#### cd 命令：切换目录

名称 | 作用
---|---
cd | 返回用户主目录
cd /etc | 改变到其它路径
cd .. | 返回到上一级目录
cd / 或 cd ～ | 返回到根目录
cd - | 返回之前的目录

#### mkdir 命令：创建文件夹

命令格式：` mkdir  [参数] [文件目录]`

-m 设置文件权限

```
 mkdir -m 777 test3     //输入
 drwxrwxrwx 2 root root 4096 10-25 17:46 test3    //实现效果
```
 
 -p 新建一个目录串
 
```
 mkdir -p /test1/test2
```
 ![](https://farm5.staticflickr.com/4776/27024999888_3e624dcbcd_o.png)

-v 创建新目录都显示信息

```
mkdir -v /test3
```
![](https://farm1.staticflickr.com/806/40895559391_9279c2cc95_o.png)

在指定目录下创建多个目录**（使用绝对路径）**

![](https://farm1.staticflickr.com/878/39590698680_863c8252b1_o.png)
 
#### rmdir 命令：删除目录

跟mkdir 相反，rmdir表示删除目录。命令格式为：`mkdir  [参数] [文件目录]` 。这个命令的特点在于，只能删除空目录；即使加了 -p 参数，也只能删除一串空目录。如果目录当中有文件，这个命令就没有用了。

#### touch 命令：创建文件

命令行格式： `touch [参数] [filename]`

选项	|命令行格式 |	作用
---|---|---
-a|	`touch -a file`	|改变档案的访问时间记录。
-d	`|touch -d time file`	|设定时间与日期，可以使用各种不同的格式。
-m	|`touch -m file`	|改变档案的修改时间记录。
-r|	`touch -r file1 file2`	|使用参考文档的时间记录。
-t	|`touch -t time file`	|设定档案的时间记录，格式与 date 指令相同。

示例：

```
// 在当前目录创建 test.txt 文件
touch test.txt

// 创建多个文件
touch test1.txt test2.txt

//修改访问时间
touch -a test.txt

//查看访问修改文件的时间
stat test.txt

// 更改为自定义格式、自定义时间戳（更改访问时间、修改时间）
touch -d '18-May-2017' test.txt

// 更改修改时间
touch -m test.txt

//修改 test1.txt 为 test2.txt 文件的时间戳。
touch -r test1.txt test2.txt

// 更改为自定义时间戳
touch -t 201703031558.28 test.txt
```

#### rm命令：删除文件或目录

用来删除文件或文件目录。命令格式为：

```
rm  [参数] [文件]
```

常用参数 | 含义
---|---
-f|--force    忽略不存在的文件，从不给出提示。
-i|--interactive 进行交互式删除
-r| --recursive，将参数中列出的全部目录和子目录均递归地删除。

**注意：**

* 如果要删除非空目录，需要使用` rm -rf [文件目录]`
* 千万不要` rm -rf /` ，一旦输入这个命令，然后就没有然后了。

