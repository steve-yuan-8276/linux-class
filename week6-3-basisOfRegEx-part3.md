---
title: 'Linux运维学习笔记6-3：正则之awk'
date: 2018-04-20 16:03:20
categories: linux
tags: [linux, regularExpression, notes] 
---

### 写在前面

这则笔记接着上一期：Linux运维学习笔记：正则之sed，继续整理正则基础知识，主要是awk 工具基础知识。


### `awk` 命令

awk 也是流式编辑器，用来进行高级文本处理，支持关联数组、归递函数、条件语句等。

#### 语法结构

##### 第一种：`awk [选项参数] 'script' file(s)`

在这种格式中，awk从一个或多个文件中读取内容，之后再用引号里的script进行处理。

`[选项参数]` 这里一般是`-F` 后面跟分割符，比如`/etc/passwd`，字段与字段之间使用`：`分割，处理时就要写成 `-F ':'`；如果不加 `-F` 默认使用空格分割。

再来说后面script的部分，awk 之所以能够进行高级文本处理，是因为这里的script可以很复杂，通常可分为三个可选的部分：BEGIN、BODY和END。

<!--more-->

###### awk的工作方式：

1. 执行BEGIN 语句块；这是一个可选语句块，例如变量初始化、打印输出表格的表头等通常都放在这里面。

2. BODY通常包含两个部分：pattern和action；awk在执行完BEGIN语句之后，会逐行读取file内容，就像一个for循环，每读一行先按照前面的pattern进行匹配，如果匹配则执行后面的action；重复这个过程，一直到文件内容全部读完。

3. 执行END语句块；跟BEGIN类似，也是可选的。

**注：**在第二步中的的action 是可以省略的；如果省略action，则默认执行{ print }，也就是把所有符合pattern的内容都打印在屏幕上。

##### 第二种：`awk [选项参数] -f script-file file(s)`

在这种格式种，script被写进脚本文件里，在执行的时候，awk 命令会从脚本文件中读取script，在按照方式一中的步骤进行操作。这则笔记暂不涉及这种用法。

#### awk 基本操作

##### 匹配字符串

- 先来看最基本的用法：从`/etc/passwd` 中匹配出含有`root` 的行；前文说到，script的部分中，BEGIN、END都是可选的，这里都省略了，中间包括了-F指定分割符，以及BODY中的pattern，省略了action，默认执行{print}，打印出了匹配pattern的行。

```
[root@stevey ~]# cat /etc/passwd | awk -F ':' '/root/'
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

- 再复杂一点，可以切割之后匹配关键词；例如，在在第一段中匹配包含keyword的行，`$1` 表示第一个字段，`~ `代表匹配的意思

```
[root@stevey ~]# awk -F ':' '$1 ~/ro/' a.txt  
root:x:0:0:root:/root:/bin/bash
[root@stevey ~]# awk -F ':' '$1 ~/oo/' a.txt
root:x:0:0:root:/root:/bin/bash
```

关于`-F `选项，后面跟指定的分割符；如不加 `-F`选项，默认以空格或tab为分隔符；print表示打印，要用{} 括起来，$1表示第一列，$2表示第二列，以此类推；

![](https://farm1.staticflickr.com/872/40775064355_ef7e11dec1_o.png)


- `{print $n} `用来指定打印区域；`$n` n表示数字，用来制定域；$0 很特殊，表示所有的段；

```
[root@stevey ~]# cat /etc/passwd |awk -F ':' '$1 ~/root/ {print $1}'     //这里只将第一字段显示出来
root

[root@stevey ~]# cat /etc/passwd |awk -F ':' '$1 ~/root/ {print $0}'    //$0表示显示整行
root:x:0:0:root:/root:/bin/bash
```

- 多次匹配

```
[root@stevey ~]# head -n 5 a.txt | awk -F ':' '/root/ {print $1,$3} /sbin/ {print $0}'
root 0
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

##### 条件操作符

awk命令支持用逻辑符号进行匹配，

符号| 含义
---|---
`==` | 等于
`!=` | 不等于
`>` | 大于
`<` | 小于
`>=` | 大于等于
`<=` | 小于等于

**注：**在于数字做比较时，加引号，则视为字符串；不加引号，则视为数字。

- 匹配uid大于500的行

```
[root@stevey ~]# awk -F ':' '$3 > 500' a.txt    //匹配uid大于500的内容
polkitd:x:999:997:User for polkitd:/:/sbin/nologin
user1:x:1000:1000::/home/user1:/bin/bash
user2:x:1001:1001::/home/user2:/bin/bash
yuanfeng:x:1002:1002::/home/yuanfeng:/bin/bash
```

- 匹配uid等于1000的行，这里相当于精确匹配

```
[root@stevey ~]# awk -F ':' '$3 == 1000' a.txt    //== 表示等于，相当于精确匹配
user1:x:1000:1000::/home/user1:/bin/bash
```

- 匹配第七列不是'/bin/bash' 的行，`!=`表示取非的意思。

```
[root@stevey ~]# awk -F ':' '$7 !=/bin/bash' a.txt
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
```

- `awk` 还可以对两列内容比较逻辑关系

```
[root@stevey ~]# awk -F ':' '$3 < $4' a.txt
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
```

- 比较两个字段是否精确比较

```
[root@stevey ~]# awk -F ':' '$3 == $4 ' a.txt
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network 
```

- 支持`&&`，表示且的意思，两个条件都满足。

```
[root@stevey ~]# awk -F ':' '$3 > 5 && $3 < 7' a.txt
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```

- 支持 `||`，表示或的意思，两个条件满足其一。

```
[root@stevey ~]# awk -F ':' '$3 > 1000 || $4 == "/bin/bash"' a.txt
user2:x:1001:1001::/home/user2:/bin/bash
yuanfeng:x:1002:1002::/home/yuanfeng:/bin/bash\
```

#### awk 内置变量

内置变量| 含义
---|---
$0	|当前记录（这个变量中存放着整个行的内容）
$1~$n	|当前记录的第n个字段，字段间由FS分隔
FS	|输入字段分隔符 默认是空格或Tab
NF	|当前记录中的字段个数，就是有多少列
NR	|已经读出的记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中。
FNR	|当前记录数，与NR不同的是，这个值会是各个文件自己的行号
RS	|输入的记录分隔符， 默认为换行符
OFS	|输出字段分隔符， 默认也是空格
ORS	|输出的记录分隔符，默认为换行符
FILENAME	|当前输入文件的名字

##### 内置变量的用法示例

- `OFS` 用法

```
[root@stevey ~]# awk -F ':' '{OFS="#"} {print $1, $3, $4}' a.txt
root#0#0
bin#1#1
daemon#2#2
adm#3#4
lp#4#7
sync#5#0
shutdown#6#0
halt#7#0
mail#8#12
```

- `NR`表示分割符后一共有多少个字段; $NF 表示最后一个字段的值

```
[root@stevey ~]# head -n 3 a.txt | awk -F ':' '{print NF}'
7
7
7
[root@stevey ~]# head -n 3 a.txt | awk -F ':' '{print $NF}'   
/bin/bash
/sbin/nologin
/sbin/nologin
```

#### awk中的数学计算

- 对指定字段赋值

```
[root@stevey ~]# awk -F ':' '{$1="root";print $0}' a.txt
root x 0 0 root /root /bin/bash
root x 1 1 bin /bin /sbin/nologin
root x 2 2 daemon /sbin /sbin/nologin
root x 3 4 adm /var/adm /sbin/nologin
root x 4 7 lp /var/spool/lpd /sbin/nologin
root x 5 0 sync /sbin /bin/sync
root x 6 0 shutdown /sbin /sbin/shutdown
```

- 将指定字段的值进行数学运算后，赋给另一个字段

```
[root@stevey ~]# head -n 2 a.txt | awk -F ':' '{$7=$3+$4; print $0}'    //$7被重新赋值，等于$3、$4相加之和。
root x 0 0 root /root 0
bin x 1 1 bin /bin 2
```

### 参考资料

* [AWK 简明教程](https://coolshell.cn/articles/9070.html)

* [GNU/Linux awk命令用法详解](https://blog.csdn.net/jasonchen_gbd/article/details/54986434)

* [linux awk命令详解](https://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html)


