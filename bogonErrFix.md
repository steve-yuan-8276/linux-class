---
title: 'Mac学习笔记：如何去掉恼人的bogon'
date: 2018-02-05 20:37:53
categories: shell
tags: [shell, bash, commandLine] 
---

### 写在前面

新买了mac，很快发现终端计算机名变成了恼人的bogon，就像这里

![Screen Shot 2018-02-05 at 8.40.01 PM](https://farm5.staticflickr.com/4650/25261705397_8033fc5277_o.png)

<!--more-->

### 原因分析

bogon这个单词是虚拟、虚伪的意思。

终端会先向 DNS 请求查询当前 IP 的反向域名解析的结果，如果查询不到再显示我们设置的计算机名。而由于我们的 DNS 错误地将保留地址反向的 NS 查询结果返回了 bogon。因此。就出现 bogon 这种奇怪的“计算机名”。

### 解决思路：

#### MacOS:

```
sudo hostname your-desired-host-name    //将your-desired-host-name 替换为你的计算机名，输入后可能会要求输入管理员密码，照做就行

sudo scutil --set LocalHostName $(hostname)

sudo scutil --set HostName $(hostname)
```
之后重启计算机，再打开终端验证一下更改是否成功。

#### CentOS 7:

```
shell> hostnamectl set-hostname centos7
shell> su
```

#### CentOS 6:

```
shell> vi /etc/sysconfig/network
//  修改以下内容
NETWORKING=yes
HOSTNAME=centos6
:wq #保存并退出
```


