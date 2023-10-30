---
title: 'Linux运维学习笔记5-2：centOS7 如何安装软件（二）'
date: 2018-04-13 16:46:43
categories: linux
tags: [centos, linux, yum, notes] 
---

### 写在前面

接着上一篇：[centOS7 如何安装软件（一）](https://www.steve-yuan.com/2018/04/13/week5-1-howToInstallSoftwareOnCentOS-part1/)，继续centos7整理软件的笔记，这要将源码包安装的问题。

**注意：**整理这期笔记的时候，遇到了超过的问题，`troubleShooting`随处可见，阅读的时候可以选择忽略`troubleShooting`，不影响学习思路。

### 源码包安装

#### 安装工作流：

* 第一步：`./configure`

* 第二步：`make`

* 第三步：`make & install`

#### 前提条件：

采用源码包安装的方式，需要用编译器进行。linux下的c语言编译器是gcc，centos下运行 `yum install -y gcc` 进行安装。

<!--more-->

#### 安装步骤：

##### 第一步：下载源代码

命令行格式： `wget [源码包地址]`

**注意：**

下载源码包一定要到官方镜像地址；

建议源码包放置在固定目录，建议存放在`/usr/local/src/`

###### 示例：尝试编译安装php源码包（对应课后练习的第9题）

php官网地址，这里选择了[香港镜像](https://secure.php.net/get/php-7.2.4.tar.bz2/from/a/mirror)下载。

![](https://farm1.staticflickr.com/805/39681503340_99d72763e7_o.png)

##### 第二步：解压源码包

命令行：tar [参数] [源码包名]

* 如果是tar.gz ，就用 -zvxf；
* 如果是tar.bz2，就用 -jxvf；

详细资料，参阅之前文件压缩工具笔记：

* [文件打包压缩工具（一）](https://www.steve-yuan.com/2018/04/11/week4-4-basisOfZipToolsPart1/)

* [文件打包压缩工具（二）](https://www.steve-yuan.com/2018/04/11/week4-5-basisOfZipToolPart2/)

示例：

```
tar -jxvf php-7.2.4.tar.bz2  //这里下载的`.tar.bz2` ，所以用`-jxvf` 解压。
```

**注：**解压完成之后务必要进入文件目录，查看一下`README` 、 INSTSLL 这两个帮助文档，能够获得很多必要的说明信息。

```
[root@stevey php-7.2.4]# less INSTALL
```

##### 第三步：配置选项并生成makefile

cd 进入源码包目录，配置选项。运行 `./configure --help | less ` 查看帮助文档；

**TroubleShooting：**运行之后，出现了错误提示：`configure: error: xml2-config not found. Please check your libxml2 installation.` 

![](https://farm1.staticflickr.com/880/39681616650_61430932d8_o.png)

google了半天，最终在[这里找到了答案](https://my.oschina.net/u/1377923/blog/617175)，解决办法：

```
yum install -y libxslt libxslt-devel libxml2 libxml2-devel
```

![](https://farm1.staticflickr.com/814/40597531615_e85037f72d_o.png)

常见的配置选择有 `--perfix=[安装目录]` ，用来自定义软件包的安装目录。

```
./configure --prefix=/usr/local/php7
```

**注：**这里的路径一定要写绝对地址，否则会报错。

配置完成后，输入 `echo $?` 进行验证，返回值为0，说明配置成功。

![](https://farm1.staticflickr.com/864/41449212892_9341c31a08_o.png)

##### 第四步：编译安装包

命令行： `make`

**TroubleShooting：** 这里有遇到了坑，错误提示：`virtual memory exhausted: Cannot allocate memory` 虚拟内存不够，又折腾了很久，找到[解决思路](https://www.cnblogs.com/chenpingzhao/p/4820814.html)

错误原因：虚拟内存不够，从而导致编译失败。

![](https://farm1.staticflickr.com/816/26620520847_d20807c982_o.png)

解决问题：涉及知识点就是[手动增加swap ]()

- 建立swapfile

```
[root@stevey php-7.2.4]# mkdir /usr/img/
[root@stevey php-7.2.4]# rm -rf /usr/img/swap
[root@stevey php-7.2.4]# dd if=/dev/zero of=/usr/img/swap bs=1024 count=2048000
2048000+0 records in
2048000+0 records out
2097152000 bytes (2.1 GB) copied, 10.1097 s, 207 MB/s
```

- 格式化swap

```
[root@stevey php-7.2.4]# mkswap /usr/img/swap
Setting up swapspace version 1, size = 2047996 KiB
no label, UUID=3f7c827e-4b1f-4120-80a2-4750c177d985
```

- 挂载swap

命令：`swapon /usr/img/swap`

![](https://farm1.staticflickr.com/805/41449980982_ef22d53ea8_o.png)

**TroubleShooting:** 好吧，真的跟打游戏一样，这里又复习一遍`chmod`的用法。

```
chmod 0600 /usr/img/swap
```

再次输入命令 `echo $?`，验证操作是否成功。

![](https://farm1.staticflickr.com/819/40779076414_4df40e7f60_o.png)

swap空间在编译完毕之后，就没用了，可以删掉。当然，也可以选择保留。

```
[root@stevey php-7.2.4]# swapoff swap 
[root@stevey php-7.2.4]# rm -f /usr/img/swap
```

第五步：安装

```
make install
```

### 关于yum的补充知识

#### 切换国内源

##### 国内源地址：

服务商| repo地址
---|---
163 | http://mirrors.163.com/.help/CentOS7-Base-163.repo
阿里巴巴|http://mirrors.163.com/.help/CentOS7-Base-163.repo

##### 实践步骤：

- 第一步：备份源地址

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

- 第二步： 下载源

```
cd /etc/yum.repos.d/       //转到源目录：
 
wget -O /etc/yum.repos.d/CentOS-163-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo     //下载源,O是大写，使用wget -O下载并以不同的文件名保存
```

- 第三步：生成缓存：

```
yum clean all     //先清空缓存
yum makecache     //再重新生成yum源
```

#### 安装拓展源epel

EPEL 是yum的一个软件源，包含了PHP、Redis、htop等基本源没有到软件。

##### epel安装

###### 方法一：yum 安装

```
[root@stevey ~]# yum install -y epel-release
```

###### 方法二：rpm安装

- 先查看系统版本

```
[root@stevey ~]# arch      //该命令用于显示当前主机的硬件架构类型
x86_64       //表示64位系统，如果显示i686就是32位系统
```

- 安装EPEL的rpm包，命令行格式：`rpm -ivh [rpm地址]`，各系统版本命令如下：

```
rpm -ivh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-8.noarch.rpm     # CentOS 7 64位
rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm         # CentOS 6 32位
rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm       # CentOS 6 64位
rpm -ivh http://dl.fedoraproject.org/pub/epel/5/i386/epel-release-5-4.noarch.rpm         # CentOS 5 32位
rpm -ivh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm       # CentOS 5 64位
```

- 检查repo源 `yum repolist` 能看到红框标出的那一行，说明以安装成功

![](https://farm1.staticflickr.com/814/26682308657_db1c44374e_o.png)

成功之后就可以使用yum 命令安装更多的命令了。

###### epel源删除（不推荐）

删除EPEL源需要同时删除配置和rpm文件。

```
rm epel.repo epel-testing.repo             # 删除配置文件
rm /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL
yum remove $(rpm -qa | grep epel)          # 删除rpm文件
yum clean all                              # 清理配置
```

#### 设置源优先级

前提条件：需要安装`yum-priorities`

![](https://farm1.staticflickr.com/786/41492423351_c4421e54a4_o.png)

安装完成后确认一下配置文件是否存在。
![](https://farm1.staticflickr.com/791/26621539347_317125e226_o.png)

编辑 /etc/yum.repos.d/目录下的*.repo 文件来设置优先级。

![](https://farm1.staticflickr.com/784/27620989868_18c79369d5_o.png)

**注意：**

1. 这里所有的repo文件都需要设置。例如 `vim /etc/yum.repos.d/CentOS-Base.repo`   在文件中另起一行写入`priority=1`，保存退出即可；其他repo也做同样修改，根据需求设置不同priority。

2. 数字越大,优先级越低

3. 推荐的设置为：

```
[base], [addons], [updates], [extras] … priority=1 

[centosplus],[contrib] … priority=2

Third Party Repos such as rpmforge … priority=N   (where N is > 10 and based on your preference)
```

#### yum下载rpm包

命令行格式：`yum install --downloadonly <package-name>`

例如，尝试下载zsh的rpm包：

![](https://farm1.staticflickr.com/795/39742008930_80689a07d2_o.png)

默认情况下，下载的rpm包保存在这里，`/var/cache/yum/x86_64/7/[repository]/packages/` 。这里的[repository]表示下载包的来源仓库的名称(例如：base、fedora、updates)，也就是上图中红圈标出来的位置。

如果想要指定下载目录，使用以下命令：

```
yum install --downloadonly --downloaddir=/tmp <package-name>    `--downloaddir`后面跟的是指定的目录
```

**注意：**

1. 在CentOS/RHEL 6或更早期的版本中，你需要安装一个单独yum插件(名称为 yum-plugin-downloadonly)才能使用--downloadonly命令；

2. 另一个专门下载安装包的软件 `yumdownloader`，这种方式只会**将指定的包下载到当前目录下，不包括依赖包**，所以实际使用起来仍然是比较麻烦的。

```
yum install yum-utils  //下载安装插件

yumdownloader <package-name>    //将指定包下载到当前目录下

```

### 参考资料：

* [配置yum源优先级](  http://ask.apelearn.com/question/7168)

* [把源码包打包成rpm包 ](http://www.linuxidc.com/Linux/2012-09/70096.htm)  

* [如何使用yum来下载RPM包而不进行安装](https://linux.cn/article-5100-1.html)

* [CentOS安装EPEL软件源](https://www.awaimai.com/1712.html)

