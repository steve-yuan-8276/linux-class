---
title: 'Linux运维学习笔记3-4：磁盘管理基础知识（一）'
date: 2018-04-04 14:14:32
categories: linux
tags: [centos, linux, notes] 
---

### 写在前面

这则笔记主要整理linux磁盘管理的基础知识，包括df、du 两条命令。

### 查看磁盘或者目录容量

#### `df `命令 disk filesystem 查看已挂载磁盘的容量

##### 命令行格式： `df [参数]`

![](https://farm1.staticflickr.com/814/41225770961_503ef1b094_o.png)

<!--more-->

如上图所示，一共有6列：

列数 | 含义
---|---
1 | 分区名字
2 | 分区总容量
3 | 使用容量
4 | 剩余容量
5 | 已使用容量所占百分比
6 | 分区挂载点

##### `df` 常见参数用法

常用的参数有`i`、 `h`、 `k`、 `m`，参数可省略。具体如下：

###### `-i` 查看inodes 使用情况，如果inodes已使用100%，即使磁盘空间仍有空余，也会提示磁盘空间已满。

![](https://lh3.googleusercontent.com/-IMWI8LRKkqI/WsSBi8VE0dI/AAAAAAABiEE/0-bGw_lxJxcnVisgLOMGno80e09R8Y24gCHMYCw/I/15228276574394.jpg)

关于inodes的更多信息，可以查看：

  * [阮一峰的笔记：理解inodes](http://www.ruanyifeng.com/blog/2011/12/inode.html)

  * [linux inode 占用100%的解决办法](http://www.dahouduan.com/2014/12/19/linux-inode-full/)

###### `-h` 使用合适的存储单位显示。

![](https://lh3.googleusercontent.com/-OPAKilt8CLY/WsSCreK2J8I/AAAAAAABiEQ/HzL92vF0KHI41_m298-d7LA6Irr4GFRSwCHMYCw/I/15228279476641.jpg)

###### `-k、-m` 指定存储单位显示，k 表示KB；m表示MB。

![](https://lh3.googleusercontent.com/-eNcvEoz5X9E/WsSC2IG1z6I/AAAAAAABiEU/tCBpOknKPnsKiBCfUbAYumAtrvBTV3SrgCHMYCw/I/15228279907551.jpg)

##### free 命令 | 查看swap 分区情况

![](https://farm1.staticflickr.com/794/39506950090_fff5cce919_o.png)

#### du 命令 disk usage 查看某个目录或者文件的大小

命令行格式 `du [参数] [dirname/filename]`

不加任何参数，只会目录（包括子目录）的大小：

![](https://lh3.googleusercontent.com/-6PsUVosd6Zw/WsSFwUQ0kzI/AAAAAAABiEk/ceOJBHZTONci0SLYPQZ3OSDN214tTaqQgCHMYCw/I/15228287345586.jpg)

##### 常用参数用法如下：

###### `-a` 列出全部文件及目录的大小

![](https://lh3.googleusercontent.com/-CTGZGx2qRB8/WsSGBgf-3oI/AAAAAAABiEo/liCA18cXV0guU278TUGoUSmzG6mejKoWwCHMYCw/I/15228288055580.jpg)

###### `-b、 -k、 -m`  指定不同的存储单位，`b` 表示` bytes` 字节；`k`代表`KB` ；`m`代表 `MB`。

![](https://lh3.googleusercontent.com/--pPLOelccUs/WsSHtJ-cbtI/AAAAAAABiE0/QVNDZnan2Cgxkk-XGJEJioicja-w7JskQCHMYCw/I/15228292328939.jpg)

**注：**使用 -k 时，如果文件小于4KB，会显示4KB（inodes记录文件存储的最小的单位是块，通常是4KB大小。）

###### `-h` 自适应恰当的输出单位

![](https://farm1.staticflickr.com/900/41182118502_f7a2323758_o.png)

**注：**这个示例是在mac上的输出结果，centos上找不到合适的目录。

###### `-c` 表示最后加总和

![](https://farm1.staticflickr.com/874/26353893207_0cc0d4f43c_o.png)

###### `-s` 表示只列出总和

![](https://farm1.staticflickr.com/864/27352848428_185185c7b1_o.png)

##### du 使用技巧

###### 参数可以连用，例如 `du -sh [dirname/filename]`

![](https://farm1.staticflickr.com/804/41226806021_8fd282234b_o.png)

###### 利用管道符，对结果进行排序 `du -h [dir_name] | sort -n`

![](https://farm1.staticflickr.com/818/39555652670_b9ef0984fb_o.png)

### 磁盘分区

#### 添加虚拟分区

点击虚拟机设置，选择磁盘，调整大小。

![](https://farm1.staticflickr.com/807/39417218650_d06e284c49_o.png)

![](https://farm1.staticflickr.com/813/40603141934_1abddd3362_o.png)

#### fdisk 磁盘分区工具使用

`fdisk `是linux 下的磁盘分区工具，只能用来划分2TB以下的分区。

##### 使用方法：

###### 一、在 console 上输入 fdisk -l /dev/sda ，观察硬盘之实体使用情形。

###### 二、在 console 上输入 fdisk /dev/sda，可进入分割硬盘模式。

![](https://farm1.staticflickr.com/869/26445115377_2691e2091f_o.png)

序号| 命令 | 含义
---|---|---
1 |m |显示所有命令
2 |p |显示硬盘分区
3 |a |设定硬盘启动区
4 |n |**设定新的硬盘分区**
5 |p |硬盘为[主要]分区(primary)
6 |e |硬盘为[延伸]分区(extend)
7 |t |改变硬盘分区属性
8 |d |**删除硬盘分区**
9 |q |**不保存退出**
10 |w |**保持并退出**

###### 实操流程：

1. 新建分区 ，按` n`

2. 设定分区类型（主分区按 `p` 或拓展分区 按 `e` ）

3. 设定分区大小，比如设定2G大小，则输入 `+2G`

4. 如果之前设定不合理，按 `d` 选择相应分区删除。

##### fdisk磁盘分区的合理原则

fdisk 分区格式为MBR，特点是最多分4个主分区，磁盘大小不能超过2T。因此使用fdisk需要遵循以下原则：

* 主分区（包括拓展分区）控制在4个以内，不得超过4个；

* 主分区在前，拓展分区在后；

* 拓展分区中划分逻辑分区（如果拓展分区被删除，其中的逻辑分区也会被删除）；

#### parted分区工具使用

前面提到`fdisk`最大支持2T的分区，大于这个分区就要使用`parted `进行分区。

parted是一个用于硬盘分区或调整分区大小的工具。使用它可以创建、清除、调整、移动和复制ext2、ext3、linux-swap、FAT、FAT32和reiserfs分区；也能创建、调整和移动苹果系统的HFS分区；还能检测jfs、ntfs、ufs和xfs分区。

该工具常用于为新安装的操作系统创建空间，重新分配硬盘使用情况，在将数据拷贝到新硬盘的时候也常常使用。

 Parted 命令分为两种模式：命令行模式和交互模式：

##### parted 安装

对于 RHEL/CentOS：

```
sudo yum install -y parted
```

对于 Debian/Ubuntu：

```
sudo apt install -y parted 
```

##### parted 启动：

命令行： `sudo parted [DeviceName]`

注：DeviceName 为指定的磁盘名称；如果不加，则启动系统默认的第一个磁盘。

如果不清楚电脑磁盘情况，输入`parted -l device ` 显示所有可用硬盘的名字，以及其它的有用信息比如储存空间、型号、扇区大小、硬盘标志以及分区信息。

##### parted 创建硬盘分区

###### 命令行模式：

` parted [option] device [command]` ,该模式可以直接在命令行下对磁盘进行分区操作。

```
parted /dev/sdb mklabel gpt mkpart 1 ext3 1 5T    
```

使用所有剩余空间创建分区

```
sudo parted /dev/sdb mkpart primary ext4 20.0GB 100%                
// 命令创建了一个 33.7 GB 的分区，从 20 GB 开始到 53 GB 结束。 100% 使用率允许用户用硬盘上所有剩余的空余空间。
```

###### 交互模式：

```
[root@localhost ~]# parted /dev/sdb   // 使用parted来对GPT磁盘操作，进入交互式模式
GNU Parted 1.8.1 Using /dev/sdb Welcome to GNU Parted! Type ‘help’ to view a list of commands.
(parted) mklabel gpt           // 将MBR磁盘格式化为GPT
(parted) print                       //打印当前分区
(parted) mkpart primary 0 4.5TB         // 分一个4.5T的主分区
(parted) mkpart primary 4.5TB 12TB     // 分一个7.5T的主分区
(parted) print                         //打印当前分区
(parted) quit     //退出
Information: Don’t forget to update /etc/fstab, if necessary. 
```

###### 列出电脑上的所有的分区，使用 `sudo parted [DeviceName] print`


```
sudo parted /dev/sdb print   //列出 `/dev/sdb` 所有分区

```

###### 分区完成之后，使用 `mkfs` 格式化后，挂载即可使用。


##### parted 命令来重新调整分区大小


###### 查询磁盘空闲空间，使用  `sudo parted [DeviceName] print free`


```
parted /dev/sdb print free   //列出 `/dev/sdb` 所有分区的空闲空间
```


###### 调整逻辑卷大小` sudo parted [Disk Name] [resizepart] [Partition Number] [Partition New End Size]`

```
sudo parted /dev/sdb resizepart 3 33.0GB   //重新调整（增加）分区 3 的结束位置，增加到 33GB

sudo parted /dev/sdb print    //确认是否扩容成功
```

###### 重新调整文件系统大小  `sudo resize2fs [DeviceName]`

```
$ sudo resize2fs /dev/sdb3
```

确认分区是否已经扩容

```
$ df -h /dev/sdb[1-3]
```

###### 删除分区 `sudo parted [Disk Name] [rm] [Partition Number]`

```
sudo parted /dev/sdb rm 3    //删除分区 3 （/dev/sdb3）

sudo parted /dev/sdb print    //查看磁盘情况，检查分区是否删除

```

###### 设置/更改分区标志  `sudo parted [Disk Name] [set] [Partition Number] [Flags Name] [Flag On/Off]`

```
sudo parted /dev/sdb set 2 lvm on  //对 /dev/sdb2 设置 lvm 标志

sudo parted /dev/sdb print   //查看更改是否成功
```

- 查看可用的标志` (parted) help set`

```
[root@localhost ~]# (parted) help set
 set NUMBER FLAG STATE change the FLAG on partition NUMBER

    NUMBER is the partition number used by Linux. On MS-DOS disk labels, the primary partitions number from 1 to 4, logical partitions from 5 onwards.
 FLAG is one of: boot, root, swap, hidden, raid, lvm, lba, hp-service, palo, prep, msftres, bios_grub, atvrecv, diag, legacy_boot, msftdata, irst, esp
 STATE is one of: on, off
```

### 扩展学习    

[parted分区gpt格式](http://www.apelearn.com/bbs/thread-7243-1-1.html)  

### 参考资料

* [理解inode](http://www.ruanyifeng.com/blog/2011/12/inode.html)

* [linux inode 占用100%的解决办法](http://www.dahouduan.com/2014/12/19/linux-inode-full/)

* [Linux系统合理规划您的硬盘分区](http://www.ruanyifeng.com/notes/2007/01/linux_3.html)

* [实例解说Linux中Fdisk分区使用方法](http://www.ruanyifeng.com/notes/2007/01/linux_fdisk.html)

* [使用parted划分GPT分区](https://my.oschina.net/guol/blog/61424)

* [Linux下使用parted分区工具为大于2T硬盘分区](https://dingxuan.info/blog/read.php/356.htm)

* [怎样用 parted 管理硬盘分区](https://linux.cn/article-9536-1.html?utm_source=rss&utm_medium=rss)





