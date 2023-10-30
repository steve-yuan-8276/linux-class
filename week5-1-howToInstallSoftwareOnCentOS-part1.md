---
title: 'Linux运维学习笔记5-1：centOS7 如何安装软件（一）'
date: 2018-04-13 16:45:44
categories: linux
tags: [centos, linux, notes, yum] 
---

### 写在前面

centOS下安装软件，主要有三种方法：rpm、yum、源码包安装。这则笔记开始，学习整理在centOS上安装软件的方法。

### rpm工具

rpm，全称是red hat package manager, 是Red Hat公司提出了软件安装管理程序。

#### 查找 `rpm` 包

rpm 包是预先在linux系统上编译打包的文件，就藏在centos7 的光盘镜像里。要使用它，首先要把这些包都找出来。

- 第一步：挂载光驱镜像

依次点击`virtual machine` -> `CD/DVD(IDE)` ->`connect CD/DVD`，选择光驱镜像

注：如果是已经加载了光驱镜像的，就会显示`disconnect CD/DVD`

![](https://farm1.staticflickr.com/891/26555432037_da1278df2e_o.png)

<!--more-->

- 第二步：将光驱挂载到 `/mnt` 目录

```
[root@stevey mnt]# mount /dev/cdrom /mnt
mount: /dev/sr0 is write-protected, mounting read-only    //出现这一行说明已经挂载上
```

- 第三步：进入/mnt 目录，查看/mnt/Packages，就能找到rpm包了。

![](https://farm1.staticflickr.com/885/40712619574_3c555dc9f2_o.png)

#### rpm 包的基本操作

##### rpm 初始化

```
[root@stevey Packages]# rpm --initdb
[root@stevey Packages]# rpm --rebuilddb
```
##### rpm包的安装、升级及删除

###### `rpm`包安装：

命令格式： `rpm [参数] [rpm包名称]`

常见参数 | 用法
---|---
-i| 安装
-v| 可视化
-h| 显示安装进度
-U| 升级rpm包
--force| 特殊用法，强制安装，即使覆盖其他包也会安装
--nodeps| 即使依赖包没有安装，也要强制安装

- 安装检测：`--test` 用来检查依赖关系,并不是真正的安装；

```
[root@stevey Packages]# rpm -ivh --test gaim-1.3.0-1.fc4.i386.rpm
```

- 安装：`rpm -ivh [rpm包名称]` 这里的包名称必须包括平台信息、版本号等一长串信息，必须吐槽一下，简直是一夜回到解放前，好土著的感觉。

```
[root@stevey Packages]# rpm -ivh gaim-1.3.0-1.fc4.i386.rpm
```

- 升级 file.rpm，使用 `-U` 参数。

```
[root@stevey Packages]#rpm -Uvh file.rpm
```

- 由新版本降级为旧版本，加`--oldpackage` 参数：

```
[root@stevey Packages]# rpm -Uvh --oldpackage gaim-1.3.0-1.fc4.i386.rpm
```

- 为软件包指定安装目录：要加 `--relocate` 参数：

```
[root@stevey Packages]# rpm -ivh --relocate /=/opt/gaim gaim-1.3.0-1.fc4.i386.rpm     //把gaim-1.3.0-1.fc4.i386.rpm指定安装在 /opt/gaim 目录中
```

注：通常情况下，centos执行文件都放在 /bin 或者 /sbin目录下，指定目录可能会导致找不到命令，解决方法是设置软链接：`ln -s [源文件] [目标文件]`


```
[root@stevey Packages]# ln -s /opt/lynx/etc/lynx.cfg /etc/lynx.cfg  
```

###### `rpm` 包卸载

命令行格式： `rpm -e [软件包名]`

```
[root@stevey Packages]# rpm -e lynx
```

**注：**这里的 rpm 包名是不包括平台信息和后缀名的。

##### rpm 包的查询

命令行格式： `rpm [参数] [rpm包名]`

以下是rpm的常见用法：

###### 查看已安装文件

- 查询系统已安装的软件: `rpm -q [软件名]`

```
[root@stevey Packages]# rpm -q gaim
```

- 查看系统中所有已经安装的包，要加 `-a` 参数 ；

```
[root@stevey Packages]# rpm -qa
```

- 在所有已经安装的软件包中查找某个软件: 与管道符 `| grep` 连用


```
[root@stevey Packages]# rpm -qa |grep gaim   //与rpm -q gaim 效果相同
```

- 查询已安装的文件属于哪个软件包：`rpm -qf [文件名]`

```
[root@stevey Packages]# rpm -qf /usr/lib/libacl.la
```
**注：**此处的文件需要使用绝对路径

- 查询已安装软件包信息：`rpm -qi [软件名]`

```
[root@stevey Packages]# rpm -qi lynx
```

- 查询已安装软件位置：`rpm -ql [软件名]` 

```
[root@stevey Packages]# rpm -ql lynx
```

- 查看已安装软件的配置文件:rpm -qc [软件名]

```
[root@stevey Packages]# rpm -qc lynx
```

- 查看已安装软件所依赖的软件包: `rpm -qR [软件名]`

```
[root@stevey Packages]# rpm -qR rpm-python
```


###### 查看未安装文件

- 查看软件包的用途及版本等信息： `rpm -qpi [软件名]`

```
[root@stevey Packages]# rpm -qpi lynx-2.8.5-23.i386.rpm
```

- 查看软件包所包含的文件：`rpm -qpl [软件名]`

```
[root@stevey Packages]# rpm -qpl lynx-2.8.5-23.i386.rpm
```

- 查看软件包的文档所在位置：`rpm -qpd [软件名]`

```
[root@stevey Packages]# rpm -qpd lynx-2.8.5-23.i386.rpm
```

- 查看软件包的配置文件：`rpm -qpc [软件名]`

```
[root@localhost RPMS]# rpm -qpc lynx-2.8.5-23.i386.rpm
```

- 查看软件包的依赖关系：`rpm -qpR [软件名]`

```
[root@localhost archives]# rpm -qpR yumex_0.42-3.0.fc4_noarch.rpm
```

### yum 工具

**注：**比rpm更智能、更人性化的软件安装管理工具，

#### 查询可用的 `yum` 包： `yum list`

![](https://farm1.staticflickr.com/810/40535478815_09b16775bf_o.png)

**注：**左侧是软件名；中间是版本信息；右侧是安装信息。

* base、anaconda表示不同的repos 仓库；

* 加上@表示已安装，不加@表示本机未安装；

* @updates 表示已安装需要升级； 

以下是一些常用的查询命令：

* 列出所有可更新的软件包：`yum list updates `

![](https://farm1.staticflickr.com/815/40593616335_cc11c4b86c_o.png)

* 列出所有已安装的软件包: `yum list installed` 


* 列出所有已安装但不在 Yum Repository 内的软件包: `yum list extras `


* 使用YUM获取软件包信息: `yum info` 


* 列出所有软件包的信息: `yum info` 


* 列出所有可更新的软件包信息: `yum info updates `


* 列出所有已安装的软件包信息: `yum info installed` 


* 列出软件包提供哪些文件: `yum provides`


#### 搜索rpm包：

##### 第一种方式： `yum search [keyword]`

![](https://farm1.staticflickr.com/791/40535597995_ace51532a7_o.png)

##### 第二种方式：`yum list | grep ‘keyword’`

![](https://farm1.staticflickr.com/800/41388102862_6e9f9a6312_o.png)

##### 第三种方式：yum provides '/*/vim'

![](https://farm1.staticflickr.com/855/27669056188_60002a2be0_o.png)

#### 软件安装卸载：

##### 基本技能

- 安装：`yum install -y [rpm 名]`

![](https://farm1.staticflickr.com/894/41388183482_fb656697b1_o.png)

- 升级 rpm 包：`yum update [-y] [rpm 名]`

- 卸载：`yum remove [rpm 名]`

注：卸载跟安装类似，卸载也可以加上`-y`参数，省略交互环节；不过稳妥起见，还是不加`-y`比较好。

##### 进阶技能

除了一个一个地安装，yum还支持一堆一堆的安装，就是按照功能把软件分组，一次安装一组软件。

- `yum grouplist` 查询

![](https://farm1.staticflickr.com/784/40645764255_5a30b39982_o.png)

-  `yum groupinstall [组名]` 安装，注意此处的组名要用单引号括起来

```
[root@stevey ~]#yum groupinstall 'Basic Web Server'
```

#### yum搭建本地仓库

相较于rpm，使用yum安装要方便很多，但是当系统无法联网，就不能使用yum安装软件了。这里的变通的方法，是搭建本地仓库。

实现办法如下：

- 第一步：挂在光驱镜像

```
mount /dev/cdrom /mnt
```

- 第二步：删除 /etc/yum.repos.d 目录下所有repos文件**（强烈建议：先备份，再删除）**

```
cp -r /etc/yum.repos.d /etc/yum.repos.d_bak //备份到/`etc/yum.repos.d_bak`

rm- rf /etc/yum.repos.d/*  /删除repos文件
```

- 第三步：创建dvd.repo

```
vim /etc/yum.repos.d/dvd.repo   //vim编辑，加入以下内容

[dvd]
name=install dvd
baseurl=file:///mnt
enabled=1
gpgcheck=0
```

- 第四步：刷新repos生成缓存

```
yum clean all     //先清楚之前到缓存
yum makecache     //生成缓存
yum list          //保险起见，可以查看一下，列表中最后一列标识为dvd的都是本地repos
```

之后，就可以使用本地repos了。

#### yum 下载安装包

##### yum只下载安装包（注：该方法适用于未安装软件）

```
yum install -y [包名] --downloadonly
```

默认情况下，下载的rpm包保存在这里：

```
/var/cache/yum/x86_64/[centos/fedora-version]/[repository]/packages     //[repository]表示下载包的来源仓库的名称(例如：base、fedora、updates)
```

上述方法，只适用于未安装软件，如果是已安装软件，则使用以下命令：

```
yum reinstall -y [包名] --downloadonly
```

下载的安装包地址在：

##### 设置保留yum安装包

```
vim /etc/yum.conf   //修改 yum 配置文件，如下：

cachedir=/home/soft/yumcache   //安装包保存地址
keepcache=1   //1表示保存已下载安装包
debuglevel=2
```

### 知识扩展

1. [yum保留已经安装过的包 ](http://www.360doc.com/content/11/0218/15/4171006_94080041.shtml)  

2. [搭建局域网yum源]( http://ask.apelearn.com/question/7627) 

### 参考资料：

[Linux rpm 命令参数使用详解](https://www.cnblogs.com/xiaochaohuashengmi/archive/2011/10/08/2203153.html)



