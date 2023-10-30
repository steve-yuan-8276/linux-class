---
title: 'linux折腾笔记：CentOS 系统的分区、启动及目录查询'
date: 2018-03-09 16:06:50
categories: linux
tags: [centos,swap, ls, bootloader, grub] 
---

### 写在前面

主要整理初次CentOS的基本操作，可能会显得比较乱。

### 系统分区

#### CentOS分区基本规则

分区名称| 空间大小
---|---
/boot| 200M
/swap | 4G
/ | 20G
/data | 剩余磁盘空间

注：如果是虚拟机仅仅用来学习linux，磁盘空间有限的情况下，处 `/boot` 、`/swap` 之外的空间都可以分给 / 根目录，没有必要再分出` /data` 了。

<!--more-->

#### swap分区是干嘛的

SWAP就是LINUX下的虚拟内存分区，主要作用有两个：

* 一是当物理内存不足以支撑系统和应用程序进程时，swap交换分区可以用作临时存放使用率不高的内存分页，把腾出的内存交给急需的应用程序进程使用。

* 二是某些程序会将初始化时残留的极少再用到的内存分页内容转移到 swap 空间，以此让出物理内存空间，防止系统崩溃。

swap分区大小跟物理内存大小有关，通常建议如下：

物理内存 | SWAP分区
---|---
4G以内| 内存的2倍
4-8G | 等于内存大小
8-64G | 8G
64-256G| 16G

### CentOS是如何启动的

#### 概括流程：
`POST` => `Boot Sequence` => `MBR(bootloader）`=> `kernel `=> `/sbin/init`

具体而言：
![](https://farm5.staticflickr.com/4778/38949748290_b5fc59518e_o.jpg)
##### POST

加电自检，检测必要的硬件设备：CPU，内存。实现自检功能主要是由镶嵌在主板芯片(CMOS)上的BIOS(basic input output system)程序，检测没问题之后进行硬件设备的初始化。

##### Boot Sequence(启动管理程序)

Boot Sequence是一个程序，它依赖于某个硬盘硬件，准确的说是第一个硬盘扇区的MBR，从而按次序查找各引导设备。

##### MBR引导，bootloader引导加载器，启动程序

MBR(Master Boot Record)：此记录在0磁道1扇区，总共为512字节，前446字节为bootloder，后64字节为分区表信息，主分区加上扩展分区不能大于四个，最后2个字节为校验信息，为55AA。

提供一个菜单，允许用户选择要启动的系统或不同的内核版本；把用户选定的内核装载到RAM中的特定空间中，解压、展开，而后把系统控制权移交给内核。

##### kernel内核实现

kernel自身初始化，实现功能，借助ramdisk探测可识别的程序，以自读方式挂载根文件系统，运行应用程序：/sbin/init

##### /sbin/init/管理用户空间服务进程

设定运行级别，进行初始化脚本，关闭或启动相应的程序，启动终端。

#### GRUB是神马东东

`GRUB（GRand Unified Bootloader）`加载内核，就是MBR中的前 446 个字节，是`BooTLoader`的一种，它的作用是要选择要启动的内核。

##### GRUB构成

主要是由device.map，menulst，stage组成：

* device.map：存放的是内核文件的根分区

* menu.lis：为菜单列表，里面为可选择的菜单列表，存放于stage2中。

* stage：用于grub引导程序过大，所以分2段引导，第一段存放在MBR中，第二段存放于内核文件系统中，第一段引导完成后可以找到第二段。 但是，第二段是存放于内核文件系统中的，此时还没有格式化文件系统，如何可以访问到第二段的menu.lst，就需要借助于中间层 stage1_5，有它来协助 stage1 段来访问stage2阶段。stage1_5通常位于stage1 字段后的 63 个扇区。 由于stage2 在内存中存放可以使用的文件系统不确定，所以这就有多个stage1_5 。


##### 更多的资料：

[CentOS系统启动流程你懂否](https://www.jianshu.com/p/fdbaab58d914)

[Centos系统启动概括流程](http://www.178linux.com/44581)

### 命令行查询系统是32位还是64位

- uname -a 

```
// 32位
Linux qs-dmm-rh2 2.6.18-194.el5 #1 SMP Tue Mar 16 21:52:43 EDT 2010 i686 i686 i386 GNU/Linux

// 64位
Linux qs-xezf-db2 2.6.18-194.el5 #1 SMP Tue Mar 16 21:52:39 EDT 2010 x86_64 x86_64 x86_64 GNU/Linux
```
 
- file /bin/ls 

```
// 32位
/bin/ls: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), for GNU/Linux 2.6.9, dynamically linked (uses shared libs), for GNU/Linux 2.6.9, stripped

// 64位
 /bin/ls: ELF 64-bit LSB executable, AMD x86-64, version 1 (SYSV), for GNU/Linux 2.6.9, dynamically linked (uses shared libs), for GNU/Linux 2.6.9, stripped
```

- getconf LONG_BIT

```
 结果直接输出32或64
```

 - arch

```
结果输出：
i686   // 32位
x86_64  //64位
```


### 目录查询：ls命令

#### 常见的重要目录

名称 | 作用
---|---
/etc| 主要保存系统所需的配置文件及子目录
/var| 系统程序的运行日志或者pid文件
/bin、usr/bin  |除root用户之外的通用账户使用的指令
/sbin、usr/sbin |供root（超级管理员）用户使用的指令

#### 目录操作的常见命令

##### cd 命令

名称 | 作用
---|---
cd | 返回用户主目录
cd /etc | 改变到其它路径
cd .. | 返回到上一级目录
cd / 或 cd ～ | 返回到根目录
cd - | 返回之前的目录

##### ls 命令

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

##### tree 命令

以目录树的形式显示文件，这个命令默认是没有预装的，需要运行` yum install tree `进行安装。

名称 | 作用
---|---
tree -a |显示所有
tree -d |仅显示目录
tree -L n |n代表数字..表示要显示几层
tree -f |显示完整路径..

**更多资料：**

[CentOS目录结构详解](https://www.cnblogs.com/zsmynl/p/3602011.html)

[CentOS常用的文件操作命令总结](http://www.haorooms.com/post/centeros_wj_zj)

