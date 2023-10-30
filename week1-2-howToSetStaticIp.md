---
title: 'Linux运维学习笔记1-2：如何配置静态IP'
date: 2018-03-14 11:57:08
categories: linux
tags: [centos, linux, ip, notes] 
---

### 主要内容

这节课主要是如何设置静态ip及过程中的网络问题排查。

### 设置网络静态IP

#### 获取IP地址信息

在登录框中输入以下命令，获取ip地址

```
dhchlient    // 自动获取ip地址
```

可以通过ping命令来验证网络是否联通

```
ping -c 4 www.163.com
```

查看当前网络状况，使用命令

```
ip addr    // 查看ip地址信息
```

`需要注意的是，这里需要记下来ip address、gateway、netmask、ensXX（网卡信息），后面设置静态ip会用到。

<!--more-->

#### 编辑网卡配置

```
# vi /etc/sysconfig/network-scripts/ifcfg-ens33
```
注：此处的XX，就是前面查到的网卡信息。

```
ONBOOT=yes；//表示网卡随系统一起启动
BOOTPROTO=static // 用来设置网卡启动类型，dhcp表示自动获取ip地址，static表示手动设置ip地址
IPADDR=xxx.xxx.xxx.xxx  //dhclient获取的网址
NETMASK=255.255.255.0    //子网掩码
GATEWAEY=xxx.xxx.xxx.xxx     //网关地址
DNS1=119.29.29.29    //DNS服务器地址，这里提供的一个公共DNS
```

最后，先按“esc”退出编辑模式，之后再输入“:wq”进行保存。


#### 重启ip设置

```
systemctl restart network.service

```
正常情况下，静态ip就设置好。检查是否成功，可以ping一下外网，如：

```
ping -c 4 www.163.com
```

### Troubleshooting

#### 网关及DNS配置错误

实际遇到的问题，按照教程进行到这一部后发现网络不通，提示错误 `name or service not know` ，解决起来又是一番折腾。归纳起来有两点：

* 之前把网关写错了，后来经网友提示，**ip和网关必须是在同一个网段；**

* 教程中给出的DNS地址 `119.29.29.29 `是DNSpod公司提供的公共DNS，如果这个DNS地址不行，就试试`8.8.8.8`，或者` 114.114.114.114`

#### 网络连接方式问题

课程中提到了不同网络连接方式也可能造成不能上网的问题，虽然没有遇到，还是上网查了一下相关的基本资料。

##### NAT模式：

就是让虚拟机借助NAT(网络地址转换)功能，通过宿主机器所在的网络来访问公网。

NAT模式中，虚拟机的网卡，是在vmware提供的一个虚拟网络，跟物理网卡的网络不在同一个网络。换句话说，宿主机可以虚拟机，虚拟机也可以访问局域网内的其他主机，但是局域网内的其他电脑不能访问虚拟机。


##### 桥接模式

桥接网络是指本地物理网卡和虚拟网卡通过VMnet0虚拟交换机进行桥接，物理网卡和虚拟网卡在拓扑图上处于同等地位，物理网卡和虚拟网卡处于同一个网段，虚拟交换机相当于一台现实网络中的交换机，所以两个网卡的IP地址也要设置为同一网段。

在这种模式下，虚拟机可以访问外网，局域网内的其他电脑也可以访问虚拟机。

##### Host-only 模式

与NAT模式类似，不同之处在于，在Host-Only模式下，虚拟网络是一个全封闭的网络，虚拟机只能与虚拟机、主机互访，但虚拟机和外部的网络是被隔离开的，不能上Internet。局域网内的计算机也不能访问虚拟机。


##### 参考资料：

* [在VMware中使CentOS利用桥接上网](https://blog.ttionya.com/article-1159.html)

* [VMware中三种网络连接的区别](https://www.cnblogs.com/rainman/archive/2013/05/06/3063925.html)

* [实例讲解虚拟机3种网络模式(桥接、nat、Host-only)](https://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646007.html)

* [VMWare（NAT、桥接 ）模式](https://www.jianshu.com/p/ed73dd4bde19)

