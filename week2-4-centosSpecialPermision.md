title:  'Linux运维学习笔记2-4：文件目录特殊权限及链接文件'
date: 2018-03-27 16:50:36
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

今天的笔记，主要总结文件目录的特殊权限，以及链接文件。

### 文件目录的特殊权限

#### set uid 

该权限针对二进位制可执行程序，可使程序文件在执行阶段拥有该程序文件所有者的权限。

这里有两个前提，缺一不可：

* 只有可执行的二进位制程序才能设定set uid权限；

* 命令执行者对该程序拥有执行（execute）权限；

比如，在Linux系统中，用户密码保存在 `/etc/shadow`  ，设置密码使用的命令是 `passwd`，分别看一下这两者的权限

![](https://farm1.staticflickr.com/886/40358699654_292fe93e3c_o.png)

由上图可知，`/etc/shadow` 默认的权限为`000`，`rwx`权限都没有的，也不能执行相应的操作。但 `passwd `赋予一个特殊权限，让我们在执行`passwd `命令时，临时拥有 root 权限，从而可以修改密码。

<!--more-->

**注意：** 

1. 虽然常见的八位制权限掩码是三位数，但其实应该是四位数，第一位表示的特殊权限。`set uid`位采用八进制表示为`4000`。

2. `passwd` 是具有`setuid`的一个特殊命令。这样我们不需要是root用户，也可以对自己的密码进行修改。

3. 设置set uid这个特殊权限很危险，不到万不得已，做好不要使用。

##### 设置setuid的方法：

###### 方法一：

```
chmod 4755 [文件名]   // 设置set uid 

chmod 755 [文件名]    // 取消 set uid
```
###### 方法二：

```
chmod u+s [文件名]   // 设置set uid 

chmod u-s [文件名]    // 取消 set uid
```

#### set gid

`set gid` 就是将程序文件的 s 权限赋予组，当执行此程序时，此进程的属组不再是运行者本人所属的基本组，而是此程序文件的属组。

setgid位八进制表示为2000。

这里有两种情况：

1. `set gid` 赋予文件：运行此文件的其它用户具有这个文件的属组特性；

2. `set gid` 赋予目录：任何用户在该目录下创建的文件，则该文件属组都和目录的属组一致。

##### 设置方法：

###### 方法一：

```
chmod 2755 [filename/dirname]   // 设置set gid 

chmod 755 [filename/dirname]    // 取消set gid
```
###### 方法二：

```
chmod g+s [filename/dirname]   // 设置set gid 

chmod g-s [filename/dirname]    // 取消 set gid
```

#### sticky bit （防删除位）

`sticky bit`（防删除位）：用户是否能够删除文件，**关键在于是否拥有文件所在目录的写权限，** 有写权限就能够删除，否则就不能。

sticky位八进制表示为1000，添加`sticky bit `（防删除位）后，用户能够该目录下添加文件，但不能删除或者重命名文件。

例如，如果我们用root用户给共享目录设置sticky bit 权限，除非root用户，其他用户只能够添加文件，但不能删除或者重命名文件。

##### 设置方法：

###### 方法一：

```
chmod 1755 [dirname]   // 设置sticky bit

chmod 755 [dirname]    // 取消sticky bit
```

###### 方法二：

```
chmod o+t [dirname]   // 设置sticky bit

chmod o-t [dirname]    // 取消sticky bit
```

### 链接文件

链接文件 ，即` link file` ，使用命令` ls -l` 查看文件信息，第一组属性首字母为` l `的文件，类似于 `windows `下的快捷方式。

链接文件` link file` 包括硬链接 `hard link` 和 软链接 `symbolic link` 两类。要搞清楚这两者的关系，需要先了解索引节点` inode `。

#### 索引节点` inode`

硬盘最小的文件单位位扇区“扇区” `Sector`，每个扇区储存512字节（相当于0.5KB）；而linux系统文件存取的最小单位，叫做块 `block`，最常见的块 `block`是4KB，由连续八个扇区 `sector`组成一个块`block`。

为了更方便的找到需要的文件，就需要有一个存储数据元信息的地方，比如文件的创建者、文件的创建日期、文件的大小等等。这种储存文件元信息的区域就叫做inode，中文译名为”索引节点”，作用就像目录索引一样。

Unix/Linux系统内部不使用文件名，而使用inode号码来识别文件。当我们打开文件时，系统大致将这个过程分为三步：

1. 找到文件名对应的inode号码； 

2. 通过inode号码，获取inode信息； 

3. 根据inode信息，找到文件数据所在的块`block`，然后读出数据。

##### inode 查询

查看某个文件的inode信息，使用命令行：

```
stat [filename]
```
#### 硬链接 `hard link`

linux系统允许多个文件名指向同一个inode号，打个比方，每个人可以有不同的名字，大名、小名、别名、外号等等，但都只向
唯一的身份ID。因此，我们可以：

* 通过不同的文件名访问文件；

* 对文件内容进行修改，会影响到所有文件名；

* 删除一个文件名，不影响另一个文件名的访问；

**注意：**新建一个空目录，默认会生成两个目录项：`. `和`..`，前者`. `等同于当前目录的”硬链接”，后者 `.. `等同于父目录的”硬链接”。所以，任何一个目录的”硬链接”总数，总是等于2加上它的子目录总数（含隐藏目录）。

##### 硬链接的不足

硬链接虽然看起来很美好，但是有两个不能克服的缺点：

1. 不能跨文件分区；每个分区在格式化之前就指定inode数据元信息存放区和文件数据存放区，inode和数据的对应关系就会在一个分区里面关联，所以不能够跨分区。

2. 不能对目录做硬链接（原因不明）；

基于上述两个先天不足，就有了软链接的用武之地。

##### ln 命令 创建硬链接

```
ln [源文件] [目标文件]
```

#### 软链接 `symbolic link` 

与硬链接不同，软链接是建立一个独立的文件。例如，文件A和文件B的inode号不一样，但A的内容是B的路径，**相当于一个路标**。

这个**路标**是真实存在的独立文件，尽管它通常都很小。读取A时，系统会自动将访问者导向B，此时A就是B的软链接`symbolic link`。

* A依赖于B而存在，如果删除了B，打开文件A就会报错：`No such file or directory`。 

* A指向B的文件名，而不是B的`inode`号码，因此B的`inode`链接数不会因此发生变化。

##### ln -s命令可以创建软链接

```
ln -s [源文文件或目录] [目标文件或目录]
```

### 参考资料

* [Linux文件特殊权限——SetUID、SetGID、Sticky BIT](https://blog.csdn.net/wxbmelisky/article/details/51649343)

* [linux中一些特殊的权限（setuid/setgid/sticky）](https://blog.csdn.net/mountzf/article/details/52033348)

* [chmod 命令 set uid ,set gid,sticky bit 说明](https://www.cnblogs.com/jary-wang/p/3149414.html)

* [硬链接、软链接及inode详解](https://blog.csdn.net/lixungogogo/article/details/52176571)

* [ linux 硬链接和软链接](https://blog.csdn.net/u012881904/article/details/72229008)

* [关于硬链接和软链接（符号链接）的区别](https://blog.csdn.net/hairetz/article/details/4168296)

* [5 分钟让你明白 “软链接” 和“硬链接”的区别](https://www.jianshu.com/p/dde6a01c4094)

