---
title: 'Linux运维学习笔记1-4：单用户模式、救援模式及两台虚拟机相互登陆'
date: 2018-03-14 15:18:51
categories: linux
tags: [centos, linux, rescueMode, notes] 
---

### 写在前面

今天笔记主要是围绕两个问题：**一是**如何更改root用户密码（单用户模式、救援模式）；**二是**两台虚拟机相互登陆。

### 单用户模式（emergency mode）

#### centos 7 进入单用户模式修改root密码：

##### 1. 重启虚拟机

![](https://farm1.staticflickr.com/798/39924649985_807fa4bfec_o.png)

<!--more-->

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

##### 1. 重启虚拟机，进入bios设置选项，默认是从硬盘启动，更改从光驱启动，保存并退出。

##### 2. 在光驱启动界面，选择`Toubleshooting`回车，使用下方向键选择 `Rescue a CentOS Linux system`，回车。

##### 3. 看到以下页面，选择第一项，回车，此时系统挂载到 /mnt/sysimage 下。

![](https://farm5.staticflickr.com/4785/40778364142_7be0228e5b_o.png)

##### 4. 输入  `chroot /mnt/sysimage `进入初始系统，输入 `passwd` 修改密码，保存。

##### 5. 按` ctrl+d` 退出初始系统，输入 `reboot `重启；进入bios 界面，设置从硬盘启动，再次重启，输入新密码进入系统。

### Linux机器相互登录

#### 克隆虚拟机

##### 1. 克隆虚拟机：点击菜单栏 `virtual machine`-\> `create full clone`

##### 2. 修改静态ip地址

```
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

##### 3. 修改主机名

```
shell\> hostnamectl set-hostname yuanfeng-02
shell\> su
```

#### 虚拟机相互登陆

##### 1. 生成密匙对（在虚拟机一上操作）

```
ssh-keygen     //生成密钥

cat /root/.ssh/id_rsa.pub    //复制的到的字符串
```

##### 2. 编辑认证文件  （在虚拟机二上操作）

```
vi /root/.shh/authrized_keys   点击I选择编辑，加上刚才复制的代码_
```

##### 3. 远程登陆 （在虚拟机一上操作）

```
ssh 192.168.222.129    //root@ 可以省略，默认登陆系统当前用户
```

登陆之后，跟在mac上用iterm2登陆虚拟机是一样样的，想怎么折腾都可以。

##### 4. 退出登陆

```
control + d
```

### 延伸阅读：

* [CentOS7中Grub2相对Grub的一些改进和注意事项](https://carey.akhack.com/2017/05/10/Grub2%E7%9B%B8%E5%AF%B9Grub%E7%9A%84%E4%B8%80%E4%BA%9B%E6%94%B9%E8%BF%9B%E5%92%8C%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9%EF%BC%88CentOS7%EF%BC%89/)

* [centos6设置grub密码](http://blog.csdn.net/cmzsteven/article/details/49049353)

* [centos-7.x 设置grub 密码](https://www.linuser.com/forum.php?mod=viewthread&tid=503)

* [CentOS 7 聚合链路及GRUB配置文件](http://blog.51cto.com/taoliang/1907976)


