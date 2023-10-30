--- 
title: 'Linux运维学习笔记1-1：centOS的安装和基本配置'
date: 2018-03-14 10:22:02
categories: linux
tags: [centos,linux, notes] 
---

## 写在前面

这则笔记主要整理linux的基础知识，主要包括以下内容：

* Linux基础知识

* centos7安装

* 初次登陆centos7

* ssh远程登陆

* 单用户模式及救援模式

* 虚拟机的备份和克隆

<!--more-->

## Linux基础知识

Linux跟MacOS、Windows一样，都是计算机操作系统，不同之处在有两点：

* 一是开源（大部分是免费的）；

* 二是多用在服务器领域;

### 常见的Linux发行版

系统名称 | 特点 | 备注
---|---|---
[Ubuntu](https://www.ubuntu.com/)|目前最流行的个人版Linux系统，界面做得比较美观。| 使用apt安装和更新软件
[Red Hat Enterprise（RHEL）](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)| 面向企业的server版本，也是应用最广泛的server版本。| 需要授权付费才能使用yum工具
[centOS](https://www.centos.org/) |CentOS与RHEL 100%兼容，更重要的是完全免费。| 使用yum工具安装和更新软件
[Debian](https://www.debian.org/)| 元老级的linux版本，也非常适合英语server| 使用apt安装和更新软件

**注：课程中使用的是centos7 64位版本，它是RHEL的开源版本，两者操作习惯一致。**

## centos安装

### 前提准备：虚拟机安装

常见的虚拟机有以下三种，**课程中选择的是虚拟机是vmware。**

虚拟机| 特点 |备注
---|---|---
[VirtualBox](https://www.virtualbox.org/wiki/Downloads)|免费，适用于mac、Windows 
[VMware fusion](https://store.vmware.com/store?Action=home&Locale=en_US&SiteID=vmware) | 付费，使用与mac和wondows|价格居中，尤其是在某宝上
[parallel desktop](https://www.parallels.com/cn/products/desktop/) |付费，适用于mac和windows |价格最贵，不过近两年都有bundle。


### 下载centOS7

注：推荐从国内的镜像网站下载安装ISO。

**下载地址：**
 
* 官方镜像：[CentOS官网](https://www.centos.org/download/) 

* 国内镜像：[搜狐](http://mirrors.sohu.com/centos/)、[163](http://mirrors.163.com/centos/)、[阿里巴巴](https://mirrors.aliyun.com/centos/)

### 安装要点：

#### 1. 系统语言：有中文设置，不过我选择了默认英文
![](https://farm5.staticflickr.com/4795/40096188544_64caf2d034_o.png)

#### 2. 时区设置：选择asia，shanghai或者hongkong都OK，反正都是东八区 
![](https://farm5.staticflickr.com/4794/40763821762_1b5ab843b3_o.png)

![](https://farm5.staticflickr.com/4786/40096214624_9fa2d544cc_o.png)

#### 3. 系统分区：
![](https://farm5.staticflickr.com/4786/39910598915_54f93524b4_o.png)

- 在系统“安装位置”中，选择“我要配置分区”，自定义系统分区；
![](https://farm5.staticflickr.com/4774/25934222407_5c921ba09b_o.png)

- 在 LVM下拉菜单，选择 “标准分区”，之后点击加号手动进行修改
![](https://farm5.staticflickr.com/4772/26935868658_9946af8702_o.png)

- 这里分了三个区： `/boot` 、`/swap` 、`/ `。除`/boot` 、`/swap`之外的空间都可以分给` / `根目录。

![](https://farm5.staticflickr.com/4788/40805785351_2fccdcbc05_o.png)

#### 4. 设置一下root用户密码，稍微复杂一点。
![](https://farm5.staticflickr.com/4794/40805799851_bc419713ca_o.png)

#### 5. 接受分区，更改，点击继续，等到安装完成。

### 安装注意事项：

#### 系统分区注意事项：

- 系统分区一般按照如下原则进行划分，但考虑到虚拟机主要是为了学习目的，总共只有20G硬盘空间，所以没有设置`/data`  分区。

分区名称| 空间大小
---|---
/boot| 200M
/swap | 4G
/ | 20G
/data | 剩余磁盘空间

- swap分区大小跟物理内存大小有关，通常建议如下：

物理内存 | SWAP分区
---|---
4G以内| 内存的2倍
4-8G | 等于内存大小
8-64G | 8G
64-256G| 16G

#### 虚拟机上网三种上网方式：

##### NAT模式：

就是让虚拟机借助NAT(网络地址转换)功能，通过宿主机器所在的网络来访问公网。

NAT模式中，虚拟机的网卡，是在vmware提供的一个虚拟网络，跟物理网卡的网络不在同一个网络。换句话说，宿主机可以虚拟机，虚拟机也可以访问局域网内的其他主机，但是局域网内的其他电脑不能访问虚拟机。


##### 桥接模式

桥接网络是指本地物理网卡和虚拟网卡通过VMnet0虚拟交换机进行桥接，物理网卡和虚拟网卡在拓扑图上处于同等地位，物理网卡和虚拟网卡处于同一个网段，虚拟交换机相当于一台现实网络中的交换机，所以两个网卡的IP地址也要设置为同一网段。

在这种模式下，虚拟机可以访问外网，局域网内的其他电脑也可以访问虚拟机。

##### Host-only 模式

与NAT模式类似，不同之处在于，在Host-Only模式下，虚拟网络是一个全封闭的网络，虚拟机只能与虚拟机、主机互访，但虚拟机和外部的网络是被隔离开的，不能上Internet。局域网内的计算机也不能访问虚拟机。

## 初次登陆centos7

初次登陆centos7，首先要做三件事：设置静态ip、设置语言环境变量、安装vim。

### 设置静态ip

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

需要注意的是，这里需要记下来ip address、gateway、netmask、ensXX（网卡信息），后面设置静态ip会用到。

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

#### Troubleshooting:网关及DNS配置错误

新手设置静态ip后，有时会出现ping不通的现象，错误提示：`name or service not know` ，此处主要从以下两个方面排查错误：

##### 网关gateway

ip和网关gateway必须是在同一个网段，否则会报错；

##### DNS

DNS服务必须要有，至少设置两组：

* DNS1:`119.29.29.29` 

* DNS1:`8.8.8.8` 或者 `8.8.4.4`

**注：**其他可供选择的还包括，`114.114.114.114` ，或者`1.1.1.1`

### 设置语言环境变量

刚安装的centos7在登陆时，经常会有一个错误提示：：`Centos warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory` ,该如何解决？

**解决方法：**修改环境变量配置文件 `/etc/environment`

```
vi /etc/environment

// 增加以下两行内容
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
```

之后，按`esc`键退出编辑状态，按`:wq` 保存退出。

### 安装vim

#### 第一步：查看相关的是不是安装了vim：

```
rpm -qa|grep vim
```
出现如下的命令

```
vim-minimal-7.4.160-1.el7.x86_64

```
#### 第二步：输入以下命令行安装：

```
yum -y install vim* 
```

#### 第三步：vim简单配置

Vim编辑环境配置 有两种方式：

1. 是在`/etc/vimrc` 进行设置，这种设置方法会作用与所有登录到Linux环境下的用户。不建议使用。 

2. 命令行 `cd ~` ，在`～`目录下创建一个` .vimrc`文件，在其中进行自己习惯的编程环境的设置，这样当别的用户使用实并不互相影响。

3. 编辑` .vimrc`文件，添加如下内容：

```
vim .vimrc     //编辑` .vimrc`文件 ，增加以下内容

set nu         // 这是设置显示行号
set  showmode   //设置在命令行界面最下面显示当前模式等。
set   ruler     // 在右下角显示光标所在的行数等信息
set autoindent   // 设置每次单击Enter键后，光标移动到下一行时与上一行的起始字符对齐
syntax on    // 即设置语法检测，当编辑C或者Shell脚本时，关键字会用特殊颜色显示
```

之后，按 `esc` 键，输入` :wq `进行保存。

## ssh远程登陆

由于服务器一般都安置在机房里，管理员一般都是通过远程进行操作，所以接下里要配置ssh免密码远程登陆。

在windows下，常用的ssh软件包括putty、xshell等；在mac下，系统自带的终端 terminal就可以，**更推荐的方案是iterm2**.

我的个人电脑是mac，所以这里重点记录iterm2免密码登陆的方法，putty、xshell大同小异，不再赘述。

### 前提准备：mac安装iterm2

#### 方法一：安装包

[官网](https://www.iterm2.com/index.html)  下载安装包，解压后拖到application文件夹即可。

#### 方法二：brew安装

```
// 先安装 brew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

// 安装brew cask
brew install caskroom/cask/brew-cask

// 安装iterm2
brew cask install iterm2
```

### ip连接虚拟机

```
// 终端输入
ssh username@xxx.xxx.xxx.xxx -p 22
```
![](https://lh3.googleusercontent.com/-051WtkX0xsE/WqjBsqnCRcI/AAAAAAABhio/soMdwI9cFFErcfmcBDxTBtPlq57Gg3z2QCHMYCw/I/15210090665231.jpg)

**注意事项**：

* 此处的username是cent os 中的账户名；

* xxx.xxx.xxx.xxx 是虚拟机刚刚设置的静态ip；

* 22 表示连接的默认端口号

### 秘钥认证连接虚拟机

**总体思路：**

- 假设有两台电脑A和B，这里的A是你要操作的电脑；B是虚拟机。

- 先在A电脑生成密匙对（公钥、私钥各一个），私钥始终保存在A电脑不动，公钥复制出来，添加到电脑B的`authorized_ keys` 里面，再进行一些必要的设置，就可以从A电脑面输入密码登陆B电脑了。

- **注意：**如果已经在A电脑上设置过keygen，比如笔者已经在搞github折腾过一回，密匙对已经存在，就不用重新生成了，只要公钥复制出来粘贴过去就可以了。

####  生成密钥对 (在A电脑操作)

##### 进入当前用户目录，一般root用户只需要这样即可

```
cd 
```
#####  创建.ssh目录并设置权限

```
mkdir -p .ssh
chmod 700 .ssh
```
##### 进入 .ssh 目录和生成密钥对

```
cd .ssh
ssh-keygen
```
然后，一路回车即可。

##### 注意事项：

* ssh-keygen 的详细用法，参见[ssh-keygen 中文手册](http://www.jinbuguo.com/openssh/ssh-keygen.html)

* 前面生成密钥的会显示地址，记住这个地址，需要使用 `cat <filename>` 查看，把id_rsa.pub 的内容复制出来，后面会用到。

#### 编辑密钥认证文件 （在B电脑操作或者通过ssh 连接B电脑操作）

##### 创建认证文件

```
touch authorized_keys
```

##### 编辑认证文件

```
vi /root/.ssh/authorized_keys
```

进入编辑界面后按i 开始编辑；把刚才复制的`id_rsa.pub ` 内容复制到里面，稳妥起见，前面可以加一行注释，注释以#开头；

##### 保存并退出

按esc退出编辑状态，再按 ` :wq` 保存退出

##### 授权认证文件

```
chmod 600 authorized_keys
```

#### 修改SSH配置（在B电脑操作或者通过ssh 连接B操作）

##### 使用root登录修改配置文件：/etc/ssh/sshd_config

```
vi /etc/ssh/sshd_config
```
##### 将下面的内容的前面的 # 去掉

```
# PubkeyAuthentication yes
# AuthorizedKeysFile .ssh/authorized_keys

//  然后，修改下面的内容的 yes 为 no

PasswordAuthentication yes
```
修改后效果为

```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
```
#### 关闭SElinux

- 临时关闭

```
setenforce 0
```

- 永久关闭

输入`vi /etc/selinux/config`，编辑这个文件，找到`SELINUX=enforcing `这一行，将`enforcing `改为`disabled`，保存退出。

#### 重启SSH服务

CentOS 6.x：

```
service sshd restart

```
CentOS 7.x：

```
systemctl restart sshd
```

#### 检验是否成功

重启虚拟机，然后使用如下命令登陆

```
ssh root@xxx.xxx.xxx.xxx
```

## 单用户模式及救援模式

### 单用户模式（emergency mode）

#### centos 7 进入单用户模式修改root密码：

##### 1. 重启虚拟机

![](https://farm1.staticflickr.com/798/39924649985_807fa4bfec_o.png)


##### 2. 三秒内按下下方向键，停留在开机选择界面，定位在第一行`CentOS Linux,with Linux 3.10.0-123.e17.x86_64`，按` e `进入编辑界面。

![](https://farm1.staticflickr.com/789/40777871842_51eab81340_o.png)

##### 3. 将 箭头所指圆心处的 ro 改为 `rw init=/sysroot/bin/bash`，之后同时按 ctrl+x，进入emergency mode。

![](https://farm5.staticflickr.com/4779/40819592551_af016aaf5e_o.png)

##### 4.` 输入 chroot /sysroot/ `，切换到centos7系统，之后输入passwd，输入新密码（两遍）即可。

![](https://farm1.staticflickr.com/790/40110816304_db495e2ed9_o.png)

**注意，**如果系统语言是中文，此处可能会显示一对框框，是语言设置的问题，解决方法是输入 `LANG=en` ，就正常了。

##### 5. 输入 `touch /.autorelabel` ,密码更高生效。之后，按 ctrl+d，在输入reboot 重启虚拟机，即可使用新密码进行登录。


#### centos 7之前的版本进单用户模式的方法：

1. Linux开机引导的时候，按键盘上的`e `进入`GRUB`菜单界面。

2. 在出现GRUB引导画面时`CentOS(2.6.18-274**)`，按字母`e`键，进入`GRUB`编辑状态。

3. 把光标移动到kernel ...那一行，再敲入 `e` 进入命令行编辑，

4. 在kernel 一行的最后加上空格single，回车

5. 敲入 `b` ，启动系统，即进入单用户模式，

6. `passwd root`修改密码。

7. `reboot`重启。

#### 给GRUB添加一把密码锁

进入单用户模式（emergency mode）固然很方便，但有的时候出于安全考虑，我们并不想让让别人随随便便修改root密码。这时候就需要给root密码加一把锁。

##### centOS 6修改grub密码的方法：

1. `vi /etc/grub.conf ` ，按 i 进入编辑模式，在`hiddemenu`下面新增一行，输入 `password=密码`，设置完成后。按 `esc` ，再输入 `:wq` 保存退出。

2. 给grub添加密码之后，再次试图进入单用户模式，须先使用 ` p ` 命 令 ，输入正确的密码后才能够对启动标签进行编辑。

##### CentOS设置grub密码

CentOS从7系列版本开始使用grub2，需要通过`grub2-setpassword` 命令来设置。

```
grub2-setpassword
Enter password:
Confirm password:
```

### 救援模式（rescue mode）

1. 重启虚拟机，进入bios设置选项，默认是从硬盘启动，更改从光驱启动，保存并退出。

2. 在光驱启动界面，选择`Toubleshooting`回车，使用下方向键选择 `Rescue a CentOS Linux system`，回车。

3. 选择第一项，回车。

4. 输入  `chroot /mnt/sysimage `进入初始系统，输入 `passwd` 修改密码，保存。

5. 按` ctrl+d` 退出初始系统，输入 `reboot `重启；进入bios 界面，设置从硬盘启动，再次重启，输入新密码进入系统。

## 虚拟机的备份和克隆

### 虚拟机备份

- 点击 ` virtual machine `，接着点击 `snapshots` 

![](https://farm1.staticflickr.com/784/26058108587_2887f191fc_o.png)

- 如下图，点击` curent state` ，之后在弹出的对话框里如数名称和注释，标识一下目前进展到什么程度了，最后点击OK就完成备份了。

![](https://farm5.staticflickr.com/4795/27059699798_6f11bbed6a_o.png)

- 想要回复到之前到备份，只要选择 `restore snashots` 即可。

![](https://farm1.staticflickr.com/813/40931404571_a6ac685450_o.png)

**注意：**备份的时机很重要，这一点跟git当中的commit有点像。建议完成一个阶段备份一次，不要太密集，浪费磁盘空间；也不要间隔时间太长，否则一旦回复，可能辛苦半天配置正确的操作也被覆盖掉了。

### 虚拟机克隆

**注意：**虚拟机克隆有两个前提必须满足，否则无法创建快照。

* 把所有的快照snapshots都删除；

* 虚拟机shutdown；

#### 第一步：点击菜单栏 `virtual machine`-\> `create full clone`

#### 第二步：修改静态ip地址

```
vi /etc/sysconfig/network-scripts/ifcfg-ens33

//修改静态ip，保存退出后重启网卡
systemctl restart network.service
```

#### 第三步：修改主机名

```
//yuanfeng-02是修改后的名字
hostnamectl set-hostname yuanfeng-02
```

## 延伸阅读：

* [CentOS7中Grub2相对Grub的一些改进和注意事项](https://carey.akhack.com/2017/05/10/Grub2%E7%9B%B8%E5%AF%B9Grub%E7%9A%84%E4%B8%80%E4%BA%9B%E6%94%B9%E8%BF%9B%E5%92%8C%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9%EF%BC%88CentOS7%EF%BC%89/)

* [centos6设置grub密码](http://blog.csdn.net/cmzsteven/article/details/49049353)

* [centos-7.x 设置grub 密码](https://www.linuser.com/forum.php?mod=viewthread&tid=503)

* [CentOS 7 聚合链路及GRUB配置文件](http://blog.51cto.com/taoliang/1907976)

* [putty和xsehll特点比较](https://www.netsarang.com/products/xsh_key_features.html)

* [手把手教给你使用putty和xshell远程连接linux](http://blog.51cto.com/13518197/2050271)

* [putty使用教程(总结)](https://www.cnblogs.com/yuwentao/archive/2013/01/06/2846953.html)

* [Putty完全使用方法](http://www.putty.ws/Putty-wanquanshiyong)

* [Putty使用教程（linux）](http://www.pubyun.com/help/index.php?title=Putty%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B%EF%BC%88linux%EF%BC%89)

* [在VMware中使CentOS利用桥接上网](https://blog.ttionya.com/article-1159.html)

* [VMware中三种网络连接的区别](https://www.cnblogs.com/rainman/archive/2013/05/06/3063925.html)

* [实例讲解虚拟机3种网络模式(桥接、nat、Host-only)](https://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646007.html)

* [VMWare（NAT、桥接 ）模式](https://www.jianshu.com/p/ed73dd4bde19)

