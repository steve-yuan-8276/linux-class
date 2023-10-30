---
title: '树莓派折腾笔记（一）：如何下载安装及基本设置'
date: 2018-03-16 13:16:36
categories: linux
tags: [树莓派, respbian] 
---

### 前提条件

* 树莓派3

* 16G TF卡 

* USB转接器

### 下载安装

#### 下载镜像

[树莓派官网](https://www.raspberrypi.org/)下载系统镜像，这里有两个选择，[Raspbian](https://www.raspberrypi.org/downloads/raspbian/) 和 [NOOBS](https://www.raspberrypi.org/downloads/noobs/)。安装官方说法，NOOBS 是给初学者使用的，an easy operating system installer which contains Raspbian，看起来不错，所以果断选择了Raspbian。

![](https://farm1.staticflickr.com/789/40126953044_f0c407f777_o.png)

<!--more-->

![](https://farm1.staticflickr.com/816/39941119695_0495d56ace_o.png)
注意，这里要选择完整版。

#### 解压镜像

下载完成后解压，得到光盘镜像文件 `2018-03-13-raspbian-stretch.img`

![](https://farm5.staticflickr.com/4771/40127027204_ee05ce7b64_o.png)

#### 烧录TF卡

##### 1. 格式化TF卡

![](https://farm5.staticflickr.com/4771/25964322327_c4c77c392d_o.png)

##### 2. 终端输入 `df`，查看当前挂载的磁盘路径，找到并记录tf路径。

##### 3. 输入 `diskutil unmount <tf卡路径> `，卸载掉树莓派的内存卡。

##### 4. 输入 `diskutil list` 查看当前内存卡的名称。

##### 5. 输入 `sudo dd bs=4m if=2018-03-13-raspbian-stretch.img of=/dev/rdisk2`，输入你的 root 密码，等待烧录完成。




参考教程：

[Mac下给SD卡安装 Raspbian 系统](https://github.com/ccforward/cc/issues/25)

[MAC下安装Raspbian](http://www.ihubin.com/blog/raspberrypi-mac-install-raspbian/)

[树莓派新手入门教程](http://www.ruanyifeng.com/blog/2017/06/raspberry-pi-tutorial.html)



