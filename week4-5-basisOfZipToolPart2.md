---
title: 'Linux运维学习笔记4-5：文件打包压缩工具（二）'
date: 2018-04-11 17:01:54
categories: linux
tags: [centos, linux, zip, notes] 
---

### 写在前面

这则笔记书接上回，继续整理打包压缩软件的知识，主要涉及`zip `和 `tar` 两个工具。

### tar 工具

由于`gzip、bzip2、xz`三个压缩工具都是针对文件进行压缩，对目录束手无策，因此一款打包工具呼之欲出，这就是`tar`。

不仅如此，`tar` 强大之处在于，配合参数，可以打包压缩一起来。

命令行格式：`tar [参数] [目标文件] [源文件或目录]`

**注：**

- `目标文件` 指的是打包后的文件名

- `源文件或目录` 指的是需要打包的文件或目录

#### 常用参数

参数| 解释
---|---
-c| 小写c，建立一个tar包或压缩包
-C| 大写C，切换到指定目录
-v| 表示可视化
-f| 指定压缩文件包名称 （多个参数组合的情况下，把f放在最后，紧跟压缩文件包名）
-t|查看tar包里的内容
-z|指定gzip压缩
-j（小写）|指定bzip2压缩
-J（大写）|指定xz压缩
-x|解包或解压缩
--exclude filename |特殊用法，表示在打包或者压缩是，将该filename排除在外

<!--more-->

#### 常见用法：

`tar `命令的参数经常连用，示例如下：

##### 文件目录打包

- `-cvf` 可视化打包目录`test`，指定文件名为`test.tar`

![](https://farm1.staticflickr.com/883/39591298120_b18c7565a1_o.png)

- `-xvf` 解包`test.tar`

![](https://farm1.staticflickr.com/898/41400341911_a94cdf644b_o.png)

- 解包到指定目录 `-C`

```
[root@stevey ~]# tar -xvf test.tar -C /root/test2/
test/
test/test1/
test/test1/a.txt
test/test2/
test/test3/
test/b.txt
```
- `--exclude filename` 特殊用法，将指定文建排除在外

当前目录的情况

![](https://farm1.staticflickr.com/817/41400575281_35e0e1c1a9_o.png)

打包压缩的情况

![](https://farm1.staticflickr.com/877/41357789462_856b0f6d51_o.png)

`-tf` 查看一下打包后的目录文件情况
![](https://farm1.staticflickr.com/874/39591660990_3ab0620310_o.png)

注：`--exclude` 支持过滤多个文件，每个文件名都要用 `--exclude` 隔开。

##### 文件打包并用gzip处理

- `-czvf` 打包文件并使用gzip压缩

![](https://farm1.staticflickr.com/811/26529628317_4e19a320e1_o.png)

- `-zxvf` 解压缩 `.tar.gz` 格式的文件

![](https://farm1.staticflickr.com/865/27530040088_2c5b940f29_o.png)

- `zcat` 查看压缩包文件内容

**注：** `zcat` 用于不真正解压缩文件，就能显示压缩包中文件的内容的场景。

![](https://farm1.staticflickr.com/880/41400794141_9261292ff8_o.png)

##### 文件打包并用bzip2处理

`-cjvf` 打包文件并使用bzip2压缩

![](https://farm1.staticflickr.com/896/27530217828_92ae2d4679_o.png)

`-xjvf` 解压缩 `.tar.bz2` 格式的文件

![](https://farm1.staticflickr.com/897/39591752720_776c467ac6_o.png)

- `bzcat` 不解压包查看内容

![](https://farm1.staticflickr.com/815/40505243565_4c549ea55a_o.png)

##### 文件打包并用xz处理

这里补充一点网上查到的资料：根据[维基百科解释](https://zh.wikipedia.org/wiki/Xz)，`xz`是一个使用 LZMA压缩算法的无损数据压缩文件格式。由于 xz 文件格式的压缩率更高，已在 Linux 各发行版中广泛使用。最典型的就是Linux内核，比如Linux内核3.12就是.xz后缀的文件，压缩包仅72.85MB，解压后能达到518.77MB。

-Jcvf 压缩文件 **注：J为大写**

```
tar -Jcvf linux-3.12
```

-Jxvf 解压缩文件 **注：J为大写**

```
tar -Jxvf linux-3.12.tar.xz
```

### `zip` 压缩工具

除了上述这些打包压缩工具，还需要考虑到在windows、linux、 mac下通用的打包压缩格式 `.zip`

zip 工具centos默认没有，需要单独安装 `yum install -y zip`

#### zip 工具的使用

##### `zip`压缩

命令行格式：`zip [参数] [压缩包名称] [源文件或目录]`

![](https://farm1.staticflickr.com/872/40505381985_0e6aaa078a_o.png)

如果目录下有还有二级甚至多级目录，zip命令 不加参数时，仅仅会讲二级目录压缩；

如果想一并压缩二级目录及多级目录，有两种方法：

- 第一种方法：使用通配符

```
zip test.zip test/*
```

- 第二种方法：使用 -r 参数

```
zip -r test.zip test/
```

##### `unzip` 解压缩

`zip `文件需要单独安装`unzip` 工具来解压，安装命令为 `yum install -y unzip `

命令行格式: `unzip [filename]`

![](https://farm1.staticflickr.com/886/40687288864_77f7950b69_o.png)

##### 补充知识点：超过4G的压缩包如何处理

linux 的unzip命令处理4G以上的压缩包会报错，解决办法是换个压缩工具，使用7zip工具。

###### 7zip 安装：

```
yum install -y wget  //先安装wget ，已经安装过的可以跳过这一步

wget https://jaist.dl.sourceforge.net/project/p7zip/p7zip/16.02/p7zip_16.02_src_all.tar.bz2  //下载安装包

tar -jxvf p7zip_16.02_src_all.tar.bz2   //解压安装包

cd p7zip_16.02     //进入安装目录

make

make install    //也可以直接输入 make & make install
```

**注意：这里遇到了两个坑**

第一，错误提示：`g++： Command not found`，如下图所示。估计是缺某个依赖包，网上查了一下，固然如此

![](https://farm1.staticflickr.com/858/39700287160_faac972ae1_o.png)

解决方案：

```
yum -y install gcc+ gcc-c++  
```

第二，也是错误提示，就是上图中的这一句：`make[1]:Leaving directory /root/p7zip_16.02/CPP/7zip/Bundles/Alone`

网上查了半天，也没查出所以然，后来试着`make install`安装了一下，输入7zip 显示如下：

```
[root@stevey p7zip_16.02]# p7zip
-bash: p7zip: command not found
```

再后来，发现命令行输错了，正确的命令行格式是 `7za [参数] [文件或目录名]`

此外，网上很多教程都提到 安装如果发现乱码,请执行命令`export LANG=zh_CN.GBK`不过，我没有遇到这个问题。

###### 7zip使用：

命令行格式：`7za [参数] [文件或目录名]`

常见用法：

```
7za a -t 7z -r [压缩包名] [压缩文件/目录]  //压缩命令，-t指定压缩类型，默认7z；-r指归递处理，表示递归所有的子文件夹

7za e [filename]   //解压到当前目录下,不保留原来的目录结构  

7za x [filename]   //解压到当前目录下,但保留原来的目录结构 
```


