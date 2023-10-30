---
title: 'Github折腾笔记：mac电脑如何配置ssh连接github'
date: 2018-01-04 20:47:46
categories: github 
tags: [ssh,github] 
---
### 准备工作 
1. 安装Apple公司的Xcode，最新版的Xcode已经默认安装好了git。完成安装之后，就可以使用 git 的命令行工具。
2. 登陆github网站申请一个免费账号。

<!--more-->

### 配置账号信息
在终端下输入以下命令行：

```
git config --global user.name <name>
git config --global user.email <mail-box>
```
<name>指的是账户名；<mail-box>指的是注册邮箱
### 创建本地ssh

SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。

#### 在终端输入以下命令行：

```
ssh-keygen -t rsa -C "<mail-box>" 
```
一路下一步，看到下图的样子说明成功
![](https://farm5.staticflickr.com/4659/26259661148_836f20c8cb_o.jpg)

#### 在终端输入：
 
```
 cat /users/admin-name/.ssh/id_rsa.pub
```
一切顺利的话，看到的信息应该是这样子的：

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDS0qLtpontavr43AQntX4oBOsg2R3QlWubMYvfgJsIDX6NWd5RaIDLBLEMwIFLDcpvpQKvk5S/bTy4vTuWqeY6fkQ/tZBKksQn1WuYDcSfjLF8BuPMfdkboTh9NaKESKnsiWdranEVbmB5vOAHm8T+HFGdzG7Tz4ygzCnTwvdpBYrd/4jgeazws2d7CuMeuyb+FxdDTQy9YmJJm+82ypfR//bLyzRJo3SYadnPO3VdOAZCO1Isky+p/0nNN/obC4t2y2+oHBAqJV9h37f9S8UShgDmZoVLicRi4poni0i70xj+t/hnOsT8fmEc+vM9USyN+ndawz2oWjikKgln1jOB 345823102@qq.com
```

#### 登陆github输入key
登陆github网站，点击Settings——SSH keys——点击右侧的Add SSH key，输入刚才得到的key

#### 验证是否配置成功 :

在终端输入：

```
ssh -T git@github.com

```
然后出现如下信息，说明连接成功

```
Warning: Permanently added the RSA host key for IP address '192.30.252.131' to the list of known hosts.

Hi hawx1993! You've successfully authenticated, but GitHub does not provide shell access.
```



