---
title: 'Linux运维学习笔记3-5：磁盘管理基础知识（二）'
date: 2018-04-08 10:17:56
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

这则笔记承接[磁盘管理基础知识（一）](https://www.steve-yuan.com/2018/04/04/week3-4-basisOfDisksetting/)，主要包括磁盘格式化、磁盘挂载及手动增加swap分区。

#### 添加一个块新硬盘的工作流：

1. 磁盘分区 （fdisk、parted）

2. 磁盘格式化  （mkfs.ext2、mkfs.ext3、mkfs.ext4和mkfs.xfs ）

3. 挂载分区  （mount）

4. 修改 `/etc/fstab`

### linux常见文件系统及磁盘格式化

#### linux常见文件系统

文件系统（File System）是文件管理系统的简称，是用来组织数据在存储介质上的存储方式以及检索方式的。


磁盘分区完成后，还需要“格式化”，其实就是安装文件系统。目前，主流操作系统的文件系统如下：

操作系统|文件系统|备注
---|---|---
windows|FAT、NTFS、ReFS|win10最新的文件系统是ReFS，相比已经被广泛应用的NTFS，弹性文件系统的优势在于能够提供更高的稳定性，可以自动验证数据是否损坏，并尽力恢复数据
linux|ext2、2xt3、ext4、xfs等|摸钱在学习的centos7的默认文件系统是xfs
MacOS|HFS、HFS+、APFS|macOS最新的文件系统是APFS，这是一种专门针对SSD优化的文件系统，最大的特性是原生支持加密。

<!--more-->

重点看看Linux常用的文件系统：

文件系统|最大文件名长度|最大文件大小|最大分区大小|适用场景|原因
---|---|---|---|---|-------
ext2|255 bytes|	2 TB	|16 TB|U盘|U盘一般不会存很多文件，且U盘的文件在电脑上有备份，安全性要求没那么高，由于ext2不写日志（journal），所以写U盘性能比较好。当然由于ext2的兼容性没有fat好，目前大多数U盘格式还是用fat
ext3|255 bytes|	2 TB	|16 TB|对稳定性要求高的地方|与ext2相比，ext3实现了日志功能，但是ext2的缺点ext3也有
ext4|255 bytes|	16 TB	|1 EB |小文件较少|ext系列的文件系统都不支持inode动态分配，所以如果有大量小文件需要存储的话，不建议用ext4
xfs|255 bytes	|8 EB	|8 EB	|小文件多或者需要大的xttr空间，如openstack swift将数据文件的元数据放在了xttr里面|xfs支持inode动态分配，所以不存在inode不够的情况，并且xttr的最大长度可以达到64K
Btrfs	|255 bytes	|16 EB	|16 EB|没有频繁的写操作，且需要btrfs的一些特性|btrfs虽然还不稳定，但支持众多的功能，如果你需要这些功能，且不会频繁的写文件，那么选择btrfs

#### 磁盘格式化

##### mke2fs、mkfs.ext2、mkfs.ext3和mkfs.ext4

**注：**默认linux这个4个命令是一样的。

命令行格式：`mke2fs [参数] [设备名称]`

常用参数 | 用法
---|---
-b | 指定数据块块大小，必须是1024的整数倍；centOS7默认是4KB
-i [字节] |i（小写）指定每个inode 的字节数
-I [文件] | I（大写）从指定的文件中读取坏的块大小
-N |设定inode的数量
-c |格式化前先检测磁盘是否有问题；加上这个选项后，格式化速度会非常慢
-L |预设该分区的标签（label）
-j |建立ext3格式的分区
-t |制定文件系统类型，如ext2、ext3、ext4等（常用）

最常用参数： `-t` 指定分区类型

```
[root@localhost ~]# mke2fs -t ext4 /dev/sdb1
```

##### mkfs.xfs 

**注：**`mke2fs` 并不支持分区格式化为xfs类型，只能使用`mkfs.xfs`

```
[root@localhost ~]# mkfs.xfs /dev/sdb1
```

##### e2label 查看或修改分区标签

**注：**该命令只支持`ext`格式的文件系统，不支持`xfs`文件系统

```
e2label /dev/sdb5    //查看分区label
e2label /dev/sdb5  TEST123   //修改分区label

```
### 挂载/卸载磁盘

磁盘格式化后，需要挂载分区才能使用。在挂载分区前，需要先建立一个挂载点，该挂载点是以目录的形式存在的，**这个挂载点必须是空目录**。一旦把某个分区挂在到该挂载点（目录）下，再往该目录写数据时，就会写到相应的分区当中。

#### 查看系统分区信息

##### `mount` 查看已挂载的分区

![](https://farm1.staticflickr.com/876/39508454940_ba483bdca2_o.png)

##### `blkid` 查看未挂载的分区

![](https://farm1.staticflickr.com/785/40604614524_759d353bbf_o.png)

##### `/etc/fstab`文件 

输入命令行 `cat /etc/fstab` ,可以查看启动时需要挂载的分区信息

![](https://farm1.staticflickr.com/889/40417612895_e0886fbcb1_o.png)

显示列 | 含义
---|---
1| 分区标示，label、UUID等
2| 挂载点，比如第1行等挂载点是`/` 根目录
3| 分区格式，如图，这里是xfs
4| mount等挂载参数（后面单列表格）
5| 是否被dump备份，1表示备份，0表示不备份
6| 开机是否自检磁盘，1和2表示检测，1的优先级高于2，即根分区设置为1，其他的分区设置为2；0表示不检测

常用参数|含义
---|---
sync/async | sync表示时时同步内存和磁盘数据；async 表示异步处理，每隔一段时间再把数据从内存写入磁盘
auto/noauto | auto开机自动挂载；noauto不自动挂载
ro/rw | ro表示以只读权限挂载；rw表示以读写权限挂载
exec/noexec | exec表示允许挂载可执行文件；noexec不允许挂载可执行文件，**`/ `根目录不要设置为`noexec`，否则无法使用系统**
user/nouser|表示是否允许root用户之外的其他用户挂载分区。**安全起见，使用`nouser`，只允许root用户挂载分区**
suid/nosuid | 是否允许分区有suid属性
usrquota|启动磁盘配额模式，会针对用户限定其使用磁盘额度
default | 按照默认值设置挂载定义，包括 rw、suid、 dev、exec、 auto、nouser和async

#### 添加挂载点的方法：

##### 方法一：vi /etc/fstab

```
/dev/sdb1       /bk          ext4            defaults,noatime       1 2
```

##### 方法二：mount [参数] [分区]

mount -a 将磁盘分区中所有的分区自动挂载上

mount -t 指定挂载分区的类型，默认不指定，会自动识别

mount -o 指定挂载分区的参数特性，即 `/etc/fstab` 第四列的内容。常见的参数比如 rw（可读写）、suid（允许设置uid）、exec（允许挂载可执行文件）等等。

##### 设置某个分区自动挂载的方法

方法一：编辑 `/etc/fstab` 中添加一行

方法二：将挂载命令写入 `/etc/rc.d/rc.local`

写入后执行如下命令：

```
chmod a+x /etc/rc.d/rc.local
```

#### 删除挂载点

命令行格式： `unmount [参数] [挂载点]`

有时挂载点无法删除，是因为当前目录在需要删除的挂载点上，解决的思路：

1. 进入其他目录执行删除操作

2. 使用-l 参数

```
unmout -l /etc/fstab
```

### 手动增加swap空间

swap分区就是虚拟内存，当虚拟内存不够的时候，就需要手动添加。基本思路是：建立swapfile -> 格式化为swap格式 -> 启动虚拟磁盘

#### 建立swapfile 

```
dd if= /dev/zero of=/tmp/newdisk bs=1M count=1024
```

`if `指定源，一般写/dev/zero 
`of` 指定目标文件 
`bs` 指定块大小
`count` 指定块的数量

根据上述内容，`目标文件大小 =  bs * count`

#### 格式化为swap格式

```
mkswap -f /tmp/newdisk
```

#### 启动虚拟磁盘

```
swapon /tmp/newdisk
```

此时，系统提示有安全风险，对该分区修改权限，使用如下命令：

```
chmod 0600 /tmp/newdisk
```

#### 卸载虚拟磁盘

```
swapoff /tmp/newdisk
```

### 参考资料

[常见文件系统的格式](https://blog.csdn.net/u013301192/article/details/47859899)

[Linux下常见文件系统对比](https://segmentfault.com/a/1190000008481493)

[Linux基础：文件系统](http://wuchong.me/blog/2014/07/19/linux-file-system/)

[鸟哥的Linux私房菜：基础学习篇 第四版](https://legacy.gitbook.com/book/wizardforcel/vbird-linux-basic-4e/details)

[Linux文件系统及磁盘分区与格式化](http://blog.51cto.com/3037673/1727744)

