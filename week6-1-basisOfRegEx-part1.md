---
title: 'Linux运维学习笔记6-1：正则之grep'
date: 2018-04-20 16:02:52
categories: linux
tags: [linux, regularExpression, notes] 
---

### 写在前面

从这则笔记开始，分3节课整理linux下正则表达式的用法，主要涉及grep、sed、awk三个命令。

正则，就是一种文本处理的方法，这里有一些[正则的基础知识](https://www.steve-yuan.com/2018/04/19/linuxNotes-BasisOfRegularExpression/#more)。

### 正则表达式

#### 基础正则表达式

正则表达式，是经过专门编写的文本字符串，用来匹配字符串，这里还整理了一篇[正则基础](https://www.steve-yuan.com/2018/04/19/linuxNotes-BasisOfRegularExpression/)。

##### 元字符

表达式|描述
---|---
`.` |匹配单个任意字符 
`[]`|匹配指定范围内的任意单个字符,比如`[abc],[a-z],[0-9],[a-zA-Z] `
`[^]`|取非，匹配指定范围外的任意单个字符 
`[:space:]`|匹配空白字符 
`[:punct:]`|匹配所有标点符号的集合 
`[a-z]`或 `[:lower:]`|匹配所有的小写字母 
`[A-Z]`或 `[:upper:]`|匹配所有的大写字母 
`[a-zA-Z]`或 `[:alpha:]`|匹配大小写字母 
`[0-9]`或 `[:digit:]`|匹配数字 
`[0-9a-zA-Z]`或 `[:alnum:]`|匹配数字和大小写字母 

<!--more-->

##### 匹配次数

表达式|描述
---|---
`*` |匹配其前面的字符任意次，比如`.*`匹配任意字符任意次数  
`\?` |匹配其前面的字符1次或0次 
`\{m,n\}` |匹配其前字符最少m，最多n次）  

##### 字符锚定

表达式|描述
---|---
`^`|锚定行首，此字符后面的任意内容必须出现在行首 
`$`|锚定行尾，此字符前面的任意内容必须出现在行尾   
`^$`|锚定空白行，可以统计空白行 
`\<`或`\b`|锚定词首，其后面的任意字符必须做为单词首部出现 
`\>`或`\b`|锚定词尾，其前面的任意字符必须做为单词尾部出现                               

#### 拓展正则表达式

相比于基础正则表达式，拓展正则表达式的不同之处主要在于以下几点：

1. 在匹配次数方面，加入了 `+` ，表示匹配字符出现一次或多次；

2. 基本正则表达式，使用`( ) { } . ? |`都需要转义,在扩展正则表达中不需要加转义符 `\`

3. `|`表示或者的意思。

### `grep`和`egrep` 命令

#### `grep `用法

`grep`是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

命令行格式：`grep [参数] [关键词] [filename]`

常用参数| 用法
---|---
-c | 打印符合要求的行数
-i | 忽略大小写
-n | 输出符合要求的行及行号
-v | 取反
-r | 归递处理，包含多级子目录
-A n| n 为数字，打印符合条件的行及后面n行
-B n| n 为数字，打印符合条件的行及前面n行
-C n| n 为数字，打印符合条件的行及前、后各n行

##### 常见用法

- 匹配符合要求的行数` grep -c 'keyword'` 

![](https://farm1.staticflickr.com/823/41663428611_5426e414db_o.png)

- 搜索`/etc`目录及其多级子目录 `grep -r /etc`

![](https://farm1.staticflickr.com/957/26796383137_6ef40a414d_o.png)

- 匹配带有关键词的行，并输出行号  `grep -n 'keyword'`    

![](https://farm1.staticflickr.com/926/41535513722_7341af2207_o.png)

- 匹配不带有关键词的行，并输出行号 `grep -v 'keyword'`

![](https://farm1.staticflickr.com/923/40865213384_8d63bca7f8_o.png)

- 匹配包括数字的行 `grep '[0-9]'`

![](https://farm1.staticflickr.com/898/26708034147_9edd8299e8_o.png)

- 匹配不包括数字的行 `grep -v '[0-9]'`

![](https://farm1.staticflickr.com/893/40865271714_03a0143a88_o.png)


- 匹配大写字母  `grep [A-Z]` 或 `grep [[:upper:]]`

![](https://farm1.staticflickr.com/826/26771685257_976bdb4c37_o.png)

- 匹配单行包含空格的内容 `grep [[:space:]]`

![](https://farm1.staticflickr.com/940/41600180172_0ebe0ceafc_o.png)

- 匹配单行包含标点符号的内容 `grep [[:punct:]]`

![](https://farm1.staticflickr.com/834/39832145770_b5bcfa81a4_o.png)

匹配单行包含数字和大小写字母的内容 `grep [0-9a-z-A-Z]`或`grep [[:alnum:]]`

![](https://farm1.staticflickr.com/787/40929599884_8b877957c0_o.png)

![](https://farm1.staticflickr.com/809/26771817517_18ea2f9f56_o.png)

- 匹配所有以`#`开头的行，并显示行号 `grep -n '^#'` **注：包括空行。**

![](https://farm1.staticflickr.com/834/27706843708_2a521350d5_o.png)

- 匹配所有的非空行和不以#开头的行 `grep -vn '^#'`

![](https://farm1.staticflickr.com/901/26709062077_fae925c39b_o.png)

- 匹配任意一个字符 `grep -n 'ro.t'` `. `表示任意字符

```
[root@stevey ~]# cat a.txt | grep -n 'ro.t'
3:root:x:0:0:root:/root:/bin/bash
```

- 匹配任意多个字符 .*  表示0个或多个任意字符；

![](https://farm1.staticflickr.com/811/26708998947_6639537642_o.png)

- 匹配特定重复次数的字符

此处需要用`{}`，其中为数字，表示为`{n}` 或` {m, n}`。其中 `{n}`表示特定次数；`{m, n}` 表示范围，`m < n`

**注：** `{} `前后都要使用转义符号`\`；如果不想使用脱义符号，使用`grep -E` 或者`egrep`


`{n}`表示特定次数；

![](https://farm1.staticflickr.com/813/40911570534_cdc0533a11_o.png)

`{m,n}` 表示范围，`m < n`

![](https://farm1.staticflickr.com/843/26753730477_39a206e94d_o.png)

`n`可以省略，表示`m`以上

![](https://farm1.staticflickr.com/788/27753256208_8f90164dcd_o.png)

`m`也可以省略，表示`n`次及以下

![](https://farm1.staticflickr.com/828/41582086382_c96f0d1885_o.png)

##### ABC特殊参数用法

- `-A n` `n`为数字，打印符合条件的行及后面`n`行

![](https://farm1.staticflickr.com/891/39814732550_f5427deaaf_o.png)

- `-B n` `n` 为数字，打印符合条件的行及前面`n`行

![](https://farm1.staticflickr.com/841/41622242561_b10c6fc037_o.png)

- `-C n` `n` 为数字，打印符合条件的行及前、后各`n`行

![](https://farm1.staticflickr.com/865/39814799080_757fba791c_o.png)


### `egrep` 命令： `grep` 命令的升级版

`egrep` 是 `grep` 的升级版，使用拓展式的正则表达式，与`grep -E `的效果相同。

- `+` 匹配一个或多个字符

![](https://farm1.staticflickr.com/876/41640348821_730ef66c52_o.png)

- `?` 匹配0个或1个字符

![](https://farm1.staticflickr.com/936/41640392701_66516eb7fc_o.png)

- `|` 表示或者，表示二选一或者多选一，满足其中一个就可以

![](https://farm1.staticflickr.com/881/39832853520_fa2cedb4b1_o.png)

- `()`表示一个整体， 比如`(oo)+`， 匹配`oo` 出现一次或一次以上 

![](https://farm1.staticflickr.com/834/26772532847_417010a494_o.png)


 


