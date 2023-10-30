---
title: 'Linux运维学习笔记2-2：环境变量、复制移动及文档查看'
date: 2018-03-20 11:37:08
categories: linux
tags: [centos, linux,  notes] 
---

### 环境变量

#### 什么是环境变量

Linux是多用户操作系统，每个用户都有自己的运行环境。这里的运行环境就是通过一组环境变量来定义。因此，我们就可以通过修改环境变量，对自己的系统运行环境进行自定义。

#### 如何查看环境变量

输入命令 `echo $PATH`

![](https://farm1.staticflickr.com/814/41068978071_84a20691fd_o.png)

#### 如何修改环境变量

例如，将“/opt/au1200_rm/build_tools/bin”加入环境变量，有三种方法：

##### 方法一：直接在shell下加入变量

```
export PATH=$PATH:/opt/au1200_rm/build_tools/bin
```

##### 方法二：修改profile文件

```
// 编辑profile配置文件
vim /etc/profile 
    
// 加入以下内容
export PATH="$PATH:/opt/au1200_rm/build_tools/bin"
```

##### 方法三：修改.bashrc文件

```
//  编辑当前用户的.bashrc 文件
 vi /~/.bashrc
 
//  加入以下内容
export PATH="$PATH:/opt/au1200_rm/build_tools/bin"
```

<!--more-->

#### 三种修改方式的特点

修改方法|特点|安全性|备注
---|---|---|---
直接在shell下加入变量|临时修改，立即生效，退出当前shell即失效|安全|只对当前账户生效
修改profile文件|永久修改，需注销用户重新登陆|不安全|更改对所有用户生效
修改.bashrc文件|永久修改，需注销用户重新登录|安全|可以精确定义每个账户

### 复制移动命令

#### cp 命令：copy

命令格式为 `cp [参数] [源文件] [目标文件]`，常见参数用法：

* `-r `复制目录及目录内的所有项目

![](https://farm1.staticflickr.com/783/27064365958_41e8d5cb77_o.png)

* `-i  `覆盖前询问

![](https://farm1.staticflickr.com/787/40227078774_24c2c183e5_o.png)

#### mv 命令：移动或重命名

命令格式为 `mv [参数] [源文件或目录] [目标文件或目录]` ，常见用法如下：

- 目标是目录，且目录一存在

![](https://lh3.googleusercontent.com/-06StOHqE9TU/WrIs4knF1SI/AAAAAAABhoE/YQU6sJP5mEYYAbCQbiMBAoYPPbTZYHMBACHMYCw/I/15216263364690.jpg)

- 目标是目录，但该目录不存在(实际相当于给文件重命名)

![](https://farm5.staticflickr.com/4794/40041669495_ba9d3081aa_o.png)

- 目标是文件，且该文件已存在(实际相当于覆盖目标文件)

![](https://farm1.staticflickr.com/794/40893873202_fb7d6eca0c_o.png)

- 目标是文件，但该文件不存在（实际相当于重命名源文件）

![](https://farm1.staticflickr.com/818/26062809817_041eae441d_o.png)

### 文档查看命令

#### `cat` 和 `tac`

```
cat <filename>
```

`cat` ：一次性显示整个文件内容



`tac` ：跟cat类似，只不过cat 是从前往后（第一行 => 最后一行），tac是从后往前（最后一行 => 第一行)。


##### 常用参数：

参数名称 | 作用
---|---
cat -n <filename> |  -n 表示显示行号
cat -A <filename>|   -A 表示显示所有内容


#### `head` 和 `tail`

这两个命令比前一组命令强的地方在于，如果文件的内容比较多，而你只需要查询文件开头或者结尾，使用这两条命令会轻松很多。

##### 常见用法：

```
head -n 10 <filename>     //显示文件开头十行
tail -n 10 <filename>     //显示文件结尾十行
```
注意：

上述示例中`n`可以省略，直接写成`head -10 <filename>`，效果也是一样的；

如果`-n` 省略，默认显示10行内容。

##### tail特殊用法：

```
tail -f <filename>
```

加 -f 参数，动态显示文件最后10行内容，在做日志监控的时候，可以方便直观的查看变化。

#### `more`和`less`

这两个命令比`head` 和 `tail`更高级，使用起来也更加方便，能够一页一页地方便使用者逐页阅读。

##### more 的用法

```
more <参数>  <filename>
```

###### 常用参数

参数名称 | 作用
---|---
d  |  在每屏的底部显示友好的提示信息  
l | 忽略 Ctrl+l （换页符）。如果没有给出这个选项，则more命令在显示了一个包含有 Ctrl+l 字符的行后将暂停显示，并等待接收命令。  
f  | 计算行数时，以实际上的行数，而非自动换行过后的行数（有些单行字数太长的会被扩展为两行或两行以上）  
p  |   显示下一屏之前先清屏。  
c  |从顶部清屏然后显示。  
s  |文件中连续的空白行压缩成一个空白行显示。  
u |不显示下划线  
/ |先搜索字符串，然后从字符串之后显示  

###### 常用操作命令

操作命令 | 作用
---|---
Enter    |向下n行，需要定义。默认为1行  
Ctrl+F   |向下滚动一屏  
space  | 向下滚动一屏  
Ctrl+B  | 返回上一屏  
=       | 输出当前行的行号  
:f     | 输出文件名和当前行的行号  
v       | 调用vi编辑器  
!  |  调用Shell，并执行命令   
q      |  退出more  

##### less  

```
less <参数>  <filename>
```

###### 常用参数

参数名称 | 作用
---|---
b |  设置缓冲区的大小  
e  | 当文件显示结束后，自动离开  
f  | 强迫打开特殊文件，例如外围设备代号、目录和二进制文件  
g  | 只标志最后搜索的关键词  
i  | 忽略搜索时的大小写  
m  | 显示类似more命令的百分比 

##### 常用操作

参数名称 | 作用
---|---
space | 翻页
j | 光标向下移动一行
k |  光标向上移动一行
/ | 按下 `/ `后输入关键词`enter`即可搜索，查看多个搜索结果时，按n进行切换。
？| 与 `/` 作用类似，不同之处在于` / `当千行向下搜索，`？`是当千行向上搜索。
 

### 参考资料：

* [linux中PATH环境变量的作用和使用方法](http://blog.sciencenet.cn/blog-1339458-804112.html)

* [Linux环境变量详解](http://blog.csdn.net/gatieme/article/details/25975465)

* [Linux环境变量总结](https://www.jianshu.com/p/ac2bc0ad3d74)



