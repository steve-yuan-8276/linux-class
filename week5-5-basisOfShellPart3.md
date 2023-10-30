---
title: 'Linux运维学习笔记5-5：shell基础知识（三）'
date: 2018-04-17 15:54:02
categories: linux
tags: [centos, linux, shell, notes] 
---

### 写在前面

这一小节是shell基础的最后一部分，主要介绍一些常用的命令，包括cut、sort、wc、uniq、tee、tr、split等。

#### cut命令：截取字段

命令行格式：`cut [参数] [filename] `

常用参数| 含义
---|---
-b |以字节（bytes）为单位进行分割。
-c |以字符（characters）为单位进行分割。
-d |自定义分隔符，默认为制表符。
-f |与-d一起使用，表示域（fields），指定显示哪个区域。

**注：必须指定 -b、-c 或 -f 之一。**

##### 以字节为单位分割

- `-b`选项，执行此命令时，cut会先把`-b`后面所有的定位进行从小到大排序，然后再提取。

`who|cut -b 3-5,8` 与 `who|cut -b 8,3-5` 一致

![](https://farm1.staticflickr.com/937/40637412185_6c5deb1e60_o.png)

<!--more-->

- `-b -3`表示从第一个字节到第三个字节；`-b 3-`表示从第三个字节到行尾；`-b -3,3-`输出整行，不会出现连续两个重叠字母。

![](https://farm1.staticflickr.com/838/41530956521_179d839f11_o.png)

##### `-c` 选项 以字符为单位分割

这里涉及到字符编码的扩展知识。在UTF-8编码中，英文字母、数字各占一个字节；汉字有的是3个字节（基本等同于GBK编码，约20000多个汉字），有的是4个字节（包括50000多个汉字）；

通过下面这个实验，就能看出`-b` 和`-c` 选项的差别。下图中那个问号，是因为遇到多字节字符，以为一个汉字占了三个字符，所以没办法完整显示。

![](https://farm1.staticflickr.com/933/40637669495_75d58bce23_o.png)

加入`-n`选项，告诉cut不要将多字节字符拆开`-nb` 和 `-c` 的效果是一样的。

![](https://farm1.staticflickr.com/820/41531462841_91148b7c00_o.png)

##### `-f` 以域为单位分割

f表示域，后面跟数字；以域为单位时，要同时使用 `-d` 后面跟分隔符，用单引号括起来。命令行格式为：`cut -d [分隔符] -f n [filename]`

- 以空格为单位，分割显示a.txt

![](https://farm1.staticflickr.com/802/41531558451_31305a1d7c_o.png)

- 与管道符连用。例如，查看`passwd`第一列的内容

![](https://farm1.staticflickr.com/871/40638020595_cdfb9093cb_o.png)

#### sort 命令： 排序

命令行格式：`sort [参数] [filename] `

常用参数|用法
---|---
 -t|后面跟分隔符，跟cut命令-d选项作用一样。
 -n|按照纯数字排序，字母和特殊符号视为0
 -r|reverse ，反向排序
 -u|去掉重复项
 -k|按照某一列的顺序进行排序
 
##### 排序的优先级规则：

除非指定`-r`参数，否则按照一下优先级顺序
 
* 以数字开头的行优先级最高；

* 以小写字母开头的行优先级次之；

* 待排序内容按字典序进行排序；
 
**注：**sort命令只做排序，并不会改动文件本身；如果需要排序后生成新的文件，可以采取输出重定向。

示例：

文件内容进行排序，默认按照字母顺序进行排序
```
[root@stevey ~]# head -n 5 /etc/passwd | cut -d ':' -f 1 > a.txt    //准备实验文件，passwd这个文件比较长，所以只取第一列、前5行的内容，重定向到a.txt
[root@stevey ~]# cat a.txt   //查看文件内容
root
bin
daemon
adm
lp
[root@stevey ~]# sort a.txt   //省略参数，默认按照英文字母顺序进行a-z进行排序
adm
bin
daemon
lp
root
```

sort还可以同时对多个文件进行排序 `sort [file1] [file2]`

```
[root@stevey ~]# tail -n 5 /etc/passwd | cut -d ':' -f 1 > b.txt
[root@stevey ~]# sort a.txt b.txt
adm
bin
daemon
lp
postfix
root
sshd
user1
user2
yuanfeng
```

`-r `参数，进行反向排序

```
[root@stevey ~]# sort -r a.txt
root
lp
daemon
bin
adm
```

-k 按照某一列进行排序，比如下面对第9列进行排序 注意此处，如果跟n参数连用的话，k放在后面跟着数字，否则会报错

![](https://farm1.staticflickr.com/875/41534204341_c0a2bac1b0_o.png)

`-n` 使用纯数字排序,下面安装第5列的数字升序排列 **注：**字母和特殊符号被视为0

![](https://farm1.staticflickr.com/856/40640632985_83953b68f5_o.png)

#### `wc `统计

命令行格式： `wc [参数] [filename] `

常用参数|用法
---|---
-l|统计行数
-m|统计字符数，包括换行符$
-L|大写L，统计字符数，不包括换行符$
-w|统计词数

示例

```
[root@stevey ~]# wc -l a.txt   //统计行数
5 a.txt
[root@stevey ~]# wc -m a.txt   //统计字符数
23 a.txt
[root@stevey ~]# wc -w a.txt   //统计词数
5 a.txt
[root@stevey ~]# cat a.txt
root
bin
daemon
adm
lp
```

- `wc `还可以跟管道符连用，可以实现很多奇妙的效果

```
[root@stevey ~]# cat /etc/passwd | wc -l   //统计一共有多少话
21
[root@stevey ~]# ll /root | wc -l   //统计当前目录一共有多少个文件/文件夹
14
```

#### `uniq` 删除重复行并显示

**注意：**

* 使用uniq前，要先用sort 进行排序，否则不起作用；

* uniq 常用参数 `-c` 表示统计重复的行数；

* uniq并不会真的删除重复项，如果确实需要删除的话，应输出重定向；

示例：

![](https://farm1.staticflickr.com/786/41493623632_9d7f6e868b_o.png)

#### `tee ` 类似输出重定向

读取标准输入的数据，并将其内容输出给文件，跟输出重定向相似，不过该命令会把输出内容显示出来。

![](https://farm1.staticflickr.com/876/26665719757_cf834ed380_o.png)

#### `tr` 替换或删除文件中的字符

常用参数|用法
---|---
-d| 删除某个字符，后面跟需要删除的字符
-s|删除重复的字符 （这里不懂，要问问清楚）

示例：

- `-d` 删除特定字符    注：这个也不是真正的删除文件，只是看起来删除了。

```
head -n 5 /etc/passwd | cut -d ':' -f 1 > a.txt
[root@stevey ~]# cat a.txt
root
bin
daemon
adm
lp
[root@stevey ~]# cat a.txt | tr -d a   
root
bin
demon
dm
lp
```

- `cat testfile |tr a-z A-Z` 或者 `cat testfile |tr [:lower:] [:upper:] `，两者作用是一样的。

![](https://farm1.staticflickr.com/936/41535069051_670ea396c5_o.png)

#### split 切割文档

命令行格式：`split [参数] [待切割文件] [切割生产文件]`

常用参数| 用法
---|---
-b|按照文件大小切割，单位为字节bytes
-l|按照行数切割

- `-b` 按照大小切割

如果不指定目标文件名，这会以xaa、xab、xac……这样的格式名来自动保存生成文件。

```
[root@stevey test1]# ls
b.txt
[root@stevey test1]# split -b 100 b.txt
[root@stevey test1]# ls
b.txt  xaa  xab  xac  xad  xae  xaf  xag  xah
```

指定文件名的命令，比如指定`yf-`，生成文件名为yf-aa、yf-ab、yf-ac…… 

![](https://farm1.staticflickr.com/826/41535296161_754313794f_o.png)

### shell特殊符号（续）

#### `$ `变量提示符
`$`  是普通用户的变量提示符，root 用户是`#`
![](https://farm1.staticflickr.com/894/41535661861_4a038c06e6_o.png)

- 与！连用，表示上条命令的最后一个参数

![](https://farm1.staticflickr.com/913/41494820022_8211321441_o.png)

#### ～ 用户家目录 

对root用户来说是/root/，普通用户是/home/username/

```
[root@stevey test1]# su - yuanfeng
Last login: Tue Apr 10 11:07:07 HKT 2018 on pts/0
[yuanfeng@stevey ~]$ pwd
/home/yuanfeng        //普通用户家目录
[yuanfeng@stevey ~]$ su -
Password:
Last login: Wed Apr 18 09:00:16 HKT 2018 from 172.16.155.1 on pts/1
[root@stevey ~]# pwd
/root          //root用户家目录
```

#### `; `适用于多条命令写在同一行，使用 `;`隔开

```
for i in `seq 1 100` ; do echo $i; done   //类似于js里面的for循环，把4条命令放在同一行书写
```

#### `&` 命令后台执行

```
[root@stevey ~]# vmstat 1 > a.txt &
[3] 76388
```

#### 错误重定向

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

#### `[] `字符组合

可以是字符组合中的任意一个，也可以表示范围

`；`、 `&&`、 `||` 三个特殊的分隔符

这三个命令都是在同一个输入两条不同命令时作为连接符使用，但是意义各有不同。假定有同一行有A、B两条command

* A ; B 相当于“and”，不管A是否执行成功，都会执行B；

* A && B 只有A执行成功，B才执行；否则，B不执行

* A || B 相当于“or”，A执行成功则B不执行；A执行不成功则B执行，A、B两条总有一条会执行。


### 延伸扩展

* [source exec 区别](http://alsww.blog.51cto.com/2001924/1113112) 

* [Linux特殊符号大全](http://ask.apelearn.com/question/7720)

* [sort并未按ASCII排序](http://blog.csdn.net/zenghui08/article/details/7938975) 

