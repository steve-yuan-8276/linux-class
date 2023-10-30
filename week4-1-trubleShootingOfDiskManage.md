---
title: 'Linux运维学习笔记4-1：磁盘管理基础知识（三）'
date: 2018-04-09 09:08:46
categories: linux
tags: [centos, linux, lvm, notes] 
---


### 写在前面

这则笔记是磁盘管理知识，主要是LVM相关知识。

### lvm：逻辑卷管理器

#### 什么是lvm？

LVM全称是Logical Volume Manager，即逻辑卷管理器。它是Linux环境下对磁盘分区进行管理的一种机制；它可以将多个物理分区整合在一起，并且可以根据实际需要动态调整文件系统空间，从而实现磁盘的动态管理。

![](https://farm1.staticflickr.com/796/27487620198_4a8888491c_o.jpg)

<!--more-->

如上图所示：

* **Disk（磁盘）：**接入系统的的物理磁盘。如图所示，就是disk A 和disk B。

* **PV 物理卷(Physical Volume)：**组成LVM的最底层的元素，即Linux上的磁盘物理分区。上图中，被分成了3个区。

* **VG 卷组(Volume Group)：**将各个独立的PV（物理卷）组合起来形成的一个存储空间就称为VG（卷组）。如上图所示，LVM总和 = VG1 + VG2

* **LV 逻辑卷(Logical Volume)：** VG被再次分割，成为可以被用户格式化、挂载并提供数据存储的对象就是LV（逻辑卷）。

由此可以看出，**创建LVM的工作流**大概是这样的：

1. 添加磁盘分区，在此基础上创建物理分区`PV`；

2. 创建卷组`VG`，将物理分区加入卷组；

3. 从卷组VG中划分逻辑卷LV，格式化挂载使用。


#### 为什么要使用lvm？

磁盘分区动态管理，实现空间充分利用。

当系统空间不足而加入新的硬盘时，不必把用户的数据从原硬盘迁 移到新硬盘，只需把新的分区加入卷组并扩充逻辑卷，不需要关机就可以实现磁盘扩容。

#### 如何创建LVM？

##### 前提准备：安装lvm2

```
yum install -y lvm2
```

##### 第一步：添加磁盘分区，在此基础上创建物理分区`PV`

###### 1.使用`fdisk [设备名称]`创建分区，按 `n `新建磁盘分区，创建过程与建立普通分区一样。

###### 2.创建完分区后，按` t` 将分区的类型改为`8e`

**注：**此处如果写错的话，按 `Ctrl + u` 撤销重新输入。

###### 3.创建PV，命令行格式 `pvcreate [device_name]`

```
pvcreate /dev/sdb1
```

###### 4.查看PV信息

- `pvs `简要查看PV信息

```
[root@stevey ~]# pvs
  PV         VG   Fmt  Attr PSize PFree
  /dev/sdb1       lvm2 ---  5.01g 5.01g
  /dev/sdb2       lvm2 ---  5.01g 5.01g
  /dev/sdc1       lvm2 ---  5.01g 5.01g
  /dev/sdc2       lvm2 ---  5.01g 5.01g
```

- `pvsdisplay [PV_NAME]`详细查看PV信息

```
[root@stevey ~]# pvdisplay /dev/sdb1
  "/dev/sdb1" is a new physical volume of "5.01 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name               
  PV Size               5.01 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               rZsvZG-nMf0-To7I-3k2X-fzEL-jkvk-eNFd6h
```

##### 第二步：创建卷组`VG`，将物理分区加入卷组；

###### 创建卷组：`vgcreate [VG_NAME] [PV_NAME]`

###### 删除卷组： `vgcreate [VG_NAME]` 

###### 查看VG信息

- `vgs `简要查看VG信息

- `vgdisplay `详细查看VG信息

###### 缩减卷组相关操作

- 确定要移除的PV，将此PV上的数据转移至其他的PV。

命令：`pvmove PV_NAME`

- 从卷组中将此PV移除

命令：`vgreduce /PATH/TO/PV`

##### 第三步：创建逻辑卷LV，格式化并挂载

###### 1.创建逻辑卷
 
``` 
  lvcreate -L SIZE -n LV_NAME VG_NAME
```

* L 指定逻辑卷的大小

* n 指定逻辑卷的名称

查看LV信息

- `lvs `简要查看VG信息。

- `lvdisplay `详细查看VG信息。

###### 2.将lv分区格式化

命令行格式： `mke2fs -t ext4 [LV_NAME]`

注：建议格式化为`ext4`格式。

###### 3.挂载lv分区挂载到某个目录使用

命令行格式：`mount [LV_NAME] [挂载点]`

**注：**没有合适的挂载点的话，可以使用`mkdir /date` 命令创建一个空目录，之后将LV分区挂载到相应的目录下。至此创建的lvm分区就可以使用了。

#### 逻辑卷`LV `的扩容和缩容

##### 扩容逻辑卷

###### 针对 `ext4` 格式

1. 卸载挂载点 `umount [LV_NAME]`

2. 重新设置逻辑卷大小 `lvresize -L [size] [LV_NAME]`

3. 检查磁盘错误 `e2fsck -f [LV_NAME]`

4. 更新逻辑卷信息 resize2fs [LV_NAME]

5. 重新挂载 `mount [LV_NAME] [挂载点]`

**示例：**

```
lvextend -L 120G /dev/mapper/centos-home     //增大至120G
lvextend -L +20G /dev/mapper/centos-home     //增加20G
lvreduce -L 50G /dev/mapper/centos-home      //减小至50G
lvreduce -L -8G /dev/mapper/centos-home      //减小8G
resize2fs /dev/mapper/centos-home            //执行调整
```

###### 针对 `xfs` 格式

执行命令：`xfs_growfs -L [size][LV_NAME]` 

```
lvextend -L 120G /dev/mapper/centos-home    //增大至120G
lvextend -L +20G /dev/mapper/centos-home    //增加20G
xfs_growfs /dev/mapper/centos-home          //执行调整
```
**注意事项：**

命令| 针对文件系统
---|---
resize2fs|针对的是ext2、ext3、ext4文件系统
xfs_growfs命令|针对的是xfs文件系统


##### 缩容逻辑卷 

缩容与之前的扩容大同小异，主要注意到地方在于，缩容之前必须先删除逻辑卷。有时，会发现删除不了

```
[root@localhost ~]# umount /home/
umount: /home: device is busy.
(In some cases useful info about processes that use
the device is found by lsof(8) or fuser(1))
```

这表示有进程占用该目录，需要先结束进程 fuser [参数] [dir_name]

```
[root@localhost ~]# fuser -m -k /home
/home: 1409 1519ce 1531e 1532e 1533e 1534e 1535e 1536e 1537e 1538e 1539e 1541e 1543e 1544e 1545e 1546e 1547e 1548e 1549e 1550e 1601m
```

之后，再次卸载就可以了。

#### 卷组 `VG`扩容工作流：

1. 生成新的物理卷PV `pvcreate [分区名]`

2. 将新扩容的物理卷加入卷组 `vgextend [VG_NAME] [PV_NAME]`

3. 重新设置逻辑卷大小（按照之前LV扩容的方法进行）

### 磁盘故障小案例

如果因为修改系统挂载点出现不能登陆的状况，只需要登录系统后，运行`vim /etc/fstab`，删除错误的配置项即可。

### 参考资料

* [Linux的LVM详解](https://blog.csdn.net/jerry_1126/article/details/45769565)

* [LVM详解](http://www.178linux.com/8227)

* [LVM 详细讲解](https://www.jianshu.com/p/eca3869e3f5c)


