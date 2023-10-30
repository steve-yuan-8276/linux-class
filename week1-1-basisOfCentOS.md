---
title: 'Linux运维学习笔记1-1：初识centOS7'
date: 2018-03-14 10:22:02
categories: linux
tags: [centos,linux, notes] 
---

### 写在前面

这是猿课linux运维的第一篇，先给自己加加油，争取四个月之内顺利毕业。

### Linux基础知识

Linux跟MacOS、Windows一样，都是计算机操作系统，不同之处在有两点：

* 一是开源（大部分是免费的）；

* 二是多用在服务器领域（学习这个就是为了转行找工作，不是嘛）

<!--more-->

### 常见的Linux发行版

系统名称| 官网 | 特点 | 备注
---|---|---|---
Ubuntu| https://www.ubuntu.com/|目前最流行的个人版Linux系统，界面做得比较美观。| 使用apt安装和更新软件
Red Hat Enterprise（RHEL）| https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux | 面向企业的server版本，也是应用最广泛的server版本。| 需要授权付费才能使用yum工具
centOS| https://www.centos.org/|CentOS与RHEL 100%兼容，更重要的是完全免费。| 使用yum工具安装和更新软件
Debian| https://www.debian.org/| 元老级的linux版本，也非常适合英语server| 使用apt安装和更新软件

### 安装虚拟机

#### 虚拟机选择：

虚拟机| 官网 | 特点 |备注
---|---|---|---
 VirtualBox| https://www.virtualbox.org/wiki/Downloads |免费，适用于mac、Windows 
VMware fusion | https://store.vmware.com/store?Action=home&Locale=en_US&SiteID=vmware| 付费，使用与mac和wondows |价格居中，尤其是在某宝上
parallel desktop | https://www.parallels.com/cn/products/desktop/ |付费，适用于mac和windows |价格最贵，不过近两年都有bundle。

### 安装centOS7

#### 下载地址：
 
* 官方镜像：[CentOS官网](https://www.centos.org/download/) 

* 国内镜像：[搜狐](http://mirrors.sohu.com/centos/)、[163](http://mirrors.163.com/centos/)、[阿里巴巴](https://mirrors.aliyun.com/centos/)

#### 新建虚拟机：

##### 安装要点：

###### 1. 系统语言：有中文设置，不过我选择了默认英文
![](https://farm5.staticflickr.com/4795/40096188544_64caf2d034_o.png)

###### 2. 时区设置：选择asia，shanghai或者hongkong都OK，反正都是东八区 
![](https://farm5.staticflickr.com/4794/40763821762_1b5ab843b3_o.png)

![](https://farm5.staticflickr.com/4786/40096214624_9fa2d544cc_o.png)

###### 3. 系统分区：
![](https://farm5.staticflickr.com/4786/39910598915_54f93524b4_o.png)

- 在系统“安装位置”中，选择“我要配置分区”，自定义系统分区；
![](https://farm5.staticflickr.com/4774/25934222407_5c921ba09b_o.png)

- 在 LVM下拉菜单，选择 “标准分区”，之后点击加号手动进行修改
![](https://farm5.staticflickr.com/4772/26935868658_9946af8702_o.png)

- 这里分了三个区： `/boot` 、`/swap` 、`/ `。除`/boot` 、`/swap`之外的空间都可以分给` / `根目录。

![](https://farm5.staticflickr.com/4788/40805785351_2fccdcbc05_o.png)

###### 4. 设置一下root用户密码，稍微复杂一点。
![](https://farm5.staticflickr.com/4794/40805799851_bc419713ca_o.png)

###### 5. 接受分区，更改，继续安装就好了。

##### 注意事项：

###### 1. 系统分区一般按照如下原则进行划分，但考虑到虚拟机主要是为了学习目的，总共只有20G硬盘空间，所以没有设置`/data`  分区。

分区名称| 空间大小
---|---
/boot| 200M
/swap | 4G
/ | 20G
/data | 剩余磁盘空间

###### 2. swap分区大小跟物理内存大小有关，通常建议如下：

物理内存 | SWAP分区
---|---
4G以内| 内存的2倍
4-8G | 等于内存大小
8-64G | 8G
64-256G| 16G




