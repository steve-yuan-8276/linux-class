---
title: 'Linux运维学习笔记6-2：正则之sed'
date: 2018-04-20 16:03:00
categories: linux
tags: [linux, regularExpression, notes] 
---


### 写在前面

这则笔记接着上一期：[Linux运维学习笔记：正则之grep](https://www.steve-yuan.com/2018/04/20/week6-1-basisOfRegEx-part1/)，主要介绍sed工具使用。

### `sed` 命令

`sed`，全称是`stream editor`，流编辑器，也是用来进行文本处理的工具。对比`grep` ，他的强大之处，不仅能够用来匹配文档内容，还能够进行修改。

`sed`在执行的时候，会从文件或者标准输入中读取一行，将其复制到缓冲区，对文本编辑完成之后，读取下一行直到所有的文本行都编辑完毕。

所以`sed`命令处理时只会改变缓冲区中文本的副本，如果想要直接编辑原文件，可以使用`-i`选项或者将结果重定向到新的文件中。

sed 命令行格式： `sed [参数] [command] [文件名] `

参数| 作用
---|---
-n	|取消默认输出，不打印不符合要求的行
p |将选择的数据列出来，通常用 `-n` 连用
-d | 删除
-e	|多点编辑，可以执行多个子命令
-f	|从脚本文件中读取命令（sed操作可以事先写入脚本，然后通过-f读取并执行）
-i	|直接编辑原文件
-l	|指定行的长度
-r	|在脚本中使用扩展正则表达式，与`grep -E`作用类似
s|用一个字符串替换另一个
w | 写入
a\ | 追加到目标行之下
i\ | 追加到目标行之上

<!--more-->

#### 使用案例

##### 文本匹配 （与`grep` 类似）

- 打印指定的某一行 `sed -n 'n'p [filename]`  注：'n'和p之间没有空格

```
[root@stevey ~]# sed -n '2'p a.txt
dvdvcfdv
```

- 打印范围，指定的某几行 `sed -n 'm,n'p [filename]`

```
[root@stevey ~]# sed -n '1,3'p a.txt   //打印第一行至第三行
#cewfef
dvdvcfdv
fewfawef
```

- 打印包含某个字符的行 `sed -n '/keyword/'p [filename]`

```
[root@stevey ~]# sed -n '/root/'p a.txt
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

- 在grep中使用的 `.  *  ^  $` ,同样也可以在sed中使用

```
[root@stevey ~]# sed -n '/ro.t/'p a.txt
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

[root@stevey ~]# sed -n '/^#/'p a.txt
#cewfef
```

- 匹配多个command `sed -e '2'p -e '/sbin/'p a.txt` 只要符合条件之一就会打印出来

```
[root@stevey ~]# sed -n -e '2'p -e '/sbin/'p a.txt
dvdvcfdv
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

- 匹配首行到末行 `sed -n '1，$'p`  `$`表示行尾

![](https://farm1.staticflickr.com/850/39857777000_c3120ff776_o.png)

##### 文本编辑 

######  `d` 删除指定内容

**注：**这里文档内容实际上并没有被删除，如果确实需要删除，还需要采取输出重定向或者使用`-i`选项。

- 删除第一行内容` sed 'n'd [filename]`  n为数字

![](https://farm1.staticflickr.com/901/39835613840_4df1c2f907_o.png)

- 删除空白行 `sed '/^$/d' [filename]`

![](https://farm1.staticflickr.com/824/39853411240_4190595323_o.png)

- 删除文件最后一行 `sed '$'d [filename]`

![](https://farm1.staticflickr.com/853/40949970984_48b9f58374_o.png)

- 删除文件中所有开头是root的行 `sed '/^root/'d [filename]`

![](https://farm1.staticflickr.com/900/41620494022_df324ff92c_o.png)

- 删除文件的第2行到末尾所有行  `sed '2,$'d [filename]`

```
[root@stevey ~]# head a.txt | sed '2,$'d   //此处删除了第2至10行的内容
#cewfef
```

###### 替换文档内容  `sed 'm,ns/keyword/替换文字/g' [filename]`

**注：**

1. 此处的`m`和`n`，代表数字，表示替换范围；

2. `s`表示替换动作，`s`和`n`之间没有空格；

3. `g`表示本行所有的`keyword`全局替换；如果不加`g`，则只替换本行第一个`keyword`

4. `/` 还可以用`#` 或者 `@` 代替

![](https://farm1.staticflickr.com/866/41643231071_ee25498abd_o.png)


###### 写入文档

- w选项：file1 中包含 keyword内容都会被写入file2  `sed -n '/root/w [file2]' [file1]`  
 
```
[root@stevey ~]# sed -n '/root/w b.txt' a.txt
[root@stevey ~]# cat b.txt
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

- `a\` : 指定内容追加到keyword 所在行后面 `sed '/^keyword/a\追加内容' file`

![](https://farm1.staticflickr.com/957/39853793990_fe7d356af4_o.png)

- 第2行之后插入 this is a test

![](https://farm1.staticflickr.com/917/41660345551_f53473004c_o.png)

- `i\` : 指定内容插入到keyword所在行之前 

![](https://farm1.staticflickr.com/932/26792835197_38df534fbb_o.png)

- 第5行之前插入 linux is blablabla

![](https://farm1.staticflickr.com/937/40950365144_f492935b28_o.png)

######  `-i` 直接编辑修改原文件

**注：** 用前面提到的替换文档内容不同，加上 -i选项，就会直接更改原始文件

![](https://farm1.staticflickr.com/816/40752239695_fe4ea3cf25_o.png)

###### 调换字符串位置

- 对字段进行排序 `sed -r 's/([^:]+):(.*):([^:]+)/\3:\2:\1/'`

```
[root@stevey ~]# head -n 3 a.txt | sed -r 's/([^:]+):(.*):([^:]+)/\3:\2:\1:/'
/bin/bash:x:0:0:root:/root:root:
/sbin/nologin:x:1:1:bin:/bin:bin:
/sbin/nologin:x:2:2:daemon:/sbin:daemon:
```

**注：**

1. `-r` 表示在脚本中使用扩展正则表达式，与`grep -E`作用类似；如果不使用`-r` ，就需要使用脱义符

2. ()表示分组，如上所述，实际上分为了三组；后面的\3、\2、\1表示分组的排序；


- 删除字符串，只保留数字 `sed -r 's/[a-zA-Z]//g'`

![](https://farm1.staticflickr.com/980/40954710974_d5a748ae8c_o.png)

- 在所有行的前面加上一个字符串aaa `sed -r 's/(.*)/aaa:&/'`

![](https://farm1.staticflickr.com/948/41625164112_0f2cf17004_o.png)


