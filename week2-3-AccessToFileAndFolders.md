---
title: 'Linux运维学习笔记2-3：文件及目录权限基础命令操作'
date: 2018-03-22 17:42:06
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

今天主要总结的centos 系统文件、目录权限及其权限操作的相关命令。

### 文件和目录权限

#### 文件主要类型

名称|释义
---|---
d| 目录
- | 普通文件
l | 链接文件
b | 块设备，例如存盘分区文件 `/dev/sda`
c | 串行端口设备文件，例如鼠标、键盘、打印机、tty终端
s | 嵌套字文件，用于进程通讯

#### 3种权限

名称 | 权限名称 | 数字描述 | 解释
---|---|---|---
r | 读权限（read）|4|可以打开文件、目录读取查看；
w | 写权限（write）|2|对文件、目录可以编写更改
x | 执行权限（execute） | 1|对文件可执行(可执行文件)、对目录可查找该目录下的内容

<!--more-->

#### 权限所属对象

名称 | 权限名称 |  解释
---|---|---
 u | 用户（user） | 生成文件或目录时登录的当前用户
 g | 组（group）| 所属群组
 0 | 其他人（others）|  除拥有者，同组人以外的人
 a |  所有人（all）| 包括拥有者、同组人及其他人

#### 举个栗子

![](https://farm1.staticflickr.com/786/40923428912_a61aa9c98b_o.jpg)

如图所示，可分为7个部分：

##### 1. 代表这个文件的类型与权限(permission)，例如第一行`-rw-r--r--.`，包含的信息包括：

* 第1位 `-` 表示这是一个普通文档；

* 第2位至第10位`rw-r--r--`，按照数字表示，也可以写为644。

* 第11位` . ` ，在centos5之前是没有这一位的；之后的版本（centos6、centos7）如果使用了SElinux content属性，这里是 ` .  `；如果使用了acl属性，这里是` + `

##### 2. 表示有多少档名连结到此节点(i-node)；

##### 3. 表示这个文件(或目录)的拥有者账号；

##### 4. 表示这个文件的所属群组；

##### 5. 表示这个文件的容量大小，默认单位为bytes；

##### 6. 表示这个文件的创建日期或最近修改日期；

##### 7. 表示这个文件的名字；

### 更改文件所属组及用户名

#### chgrp  更改所属组（change group）

命令行格式： `chgrp [组名] [文件名/目录名]`

##### 更改文件所属组

命令行格式： `chgrp [组名] [文件名]`

如下图所示，所属组由 `root `改为` test-1-group`

![](https://farm1.staticflickr.com/788/40072167445_23485bbd1e_o.png)

##### 更改目录所属组

命令行格式： `chgrp [组名] [目录名]`

默认只更改目录所属组，如下图所示，文件夹a1所属组已经由`root` 改为 `test-1-group`；而文件夹 `a1 `中的` b2` 所属组仍为 `root `，并未改变。

![](https://farm5.staticflickr.com/4786/26094089267_bce206c3e3_o.png)

如果希望一次性都改掉，则需要使用参数 `-R`  ，格式为 `chgrp -R [所属组] [目录名]`

![](https://farm1.staticflickr.com/817/40966123091_20599b2c30_o.png)

**注：**配图命令有点问题，应该是 `chgrp - R test-1-group a1`

#### chown 更改所有者和所属组

chown (change owner) 既可以更改文件所有者，也可以连同所属组一起更改。具体用法：

##### 更改所有者：`chown [用户名] [文件名]`

![](https://farm1.staticflickr.com/787/27153315418_ce768e280c_o.png)

##### 同时更改所有者和所属组： `chown [ -R ] [用户名：组名] [文件名或目录名]`

![](https://farm1.staticflickr.com/800/40131059235_46bca59141_o.png)

### 更改文件权限

#### chmod 更改文件权限

##### 第一种方式：命令格式：` chmod [ -R ] [number ] [文件名或目录]`

如前文所述，在描述文件权限的时候，可以用4代表r（read）、2代表w（write）、x代表 execute（执行）；

在linux中，目录的默认权限为755，即（`user:rwx`；`group:rx`；`others:rx`）；文件的默认权限为644 ，即（`user:rw`；`group:r`；`others:r`）

这里的`-R` 是可选项，表示级联修改，` chmod [ -R ] [number ] [目录]` ， 不但会更改目录权限，连同目录下的文件权限也一并更改。

##### 第二种方式：针对某个分组设置权限

文件所有者：`chmod u+x a1/1.txt`

![](https://farm1.staticflickr.com/784/40131521215_0f53f2e6cd_o.png)

文件所属组：`chmod o=rwx a1/1.txt`

![](https://farm1.staticflickr.com/819/27153741948_d45a591c08_o.png)

其他用户：`chmod o-x a1/1.txt`

![](https://farm1.staticflickr.com/791/27153813428_7a76d17e4e_o.png)

所有用户：`chmod a+x a1/1.txt`

![](https://farm1.staticflickr.com/807/40316354744_02eb9dd508_o.png)

注意：a代表all（所有组别用户）

#### umask 文件默认权限

**WARNING： 一般不用修改文件默认权限，因此这一小节仅作为了解内容。**

默认情况下，目录的权限为755，即 `rwxr-xr-x` ;默认普通文件的权限为644，即 `rw-r--r--` ；

输入 `umask` ，可以查询默认权限；root账户的默认值为root账户为`0022`，一般用户为`0002`。以root用户为例，是将数字转为rwx，之后再“减去”相应的全选。具体而言，

* 第一位表示这是一个八进位制数字，没有特别含义，不用管它；

* 此时文件夹权限为777 - 022 = 755，即`rwxrwxrwx` - `----w--w-` = `rwxr-xr-x` 

* 此时文件权限为666 - 022 = 644 ，即`rw-rw-rw-` - `----w--w-` = `rw- r--r--`

文件默认权限，可以通过 `umask [number] `来更改,方法参照前面的例子即可。

### 修改文件的特殊权限

#### chatrr 命令

命令格式 `chatrr [ +-= ] [ 参数 ] [文件或目录名]`

##### 常见参数用法

命令|含义
---|---
A | 文件或目录的atime不可修改
a | 只能追加不能删除，非root用户不能设定该属性
s | 会将数据同步写入磁盘
c | 自动压缩文件，读取时自动解压
i | 不能删除、重命名、设定链接、编辑写入及新增数据

##### 常用用法示例

###### `a` 用法

```
# touch a.txt
# ls
anaconda-ks.cfg  a.txt
# chattr +a a.txt
# rm -rf a.txt
rm: cannot remove ‘a.txt’: Operation not permitted //不可删除
# echo 'this is a test' >> a.txt  //只能追加内容
# cat a.txt
this is a test
```

**注：**删除`a`权限用 ` chattr -a a.txt`

###### `i` 用法

```
 # touch b.txt
 # ls
anaconda-ks.cfg  b.txt
 # chattr +i b.txt
 # rm -rf b.txt     //不可删除
rm: cannot remove ‘b.txt’: Operation not permitted
 # echo 'this is a test' > b.txt    //不可编辑写入
-bash: b.txt: Permission denied
 # echo 'this is a test' >> b.txt     //不可追加内容
-bash: b.txt: Permission denied
```

**注：**删除`i `权限 用` chattr -i b.txt`

#### lsattr 命令

`lsattr` 用来读取文件或目录的特殊权限，命令格式为 `lsattr [ 参数 ] [文件或目录名]`

参数 | 含义
---|---
a | 类似于`ls -a`  ，表示连同隐藏文件一起列出
R | 连同子目录的信息一起列出

举个栗子

```
 # mkdir c1 
 # lsattr c1    // c1是刚刚创建的一个空文件夹，因此里面没有文件，不会又显示
 # lsattr -aR c1  //连同隐藏文件一起显示
---------------- c1/.
---------------- c1/..
```

### 参考资料

* [Linux更改文件及目录权限](https://www.iwwenbo.com/linux-file-dir-chmod-chown/)

* [linux文件目录权限](https://www.jianshu.com/p/37248be468f1)

* [Linux下文件及目录普通权限](http://blog.51cto.com/yuan2/93318)

* [linux用户组和权限管理](http://www.178linux.com/83210)


