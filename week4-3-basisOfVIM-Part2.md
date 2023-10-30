---
title: 'Linux运维学习笔记4-3：vim基础知识（二）'
date: 2018-04-09 16:24:47
categories: linux
tags: [centos, linux, notes, vim] 
---

### 写在前面

这则笔记继续整理学习VIM用法，主要包括编辑模式、命令模式相关内容。

- 先把vim命令贴出来：

![](https://farm5.staticflickr.com/4783/39120869320_db56e0d91c_o.jpg)

<!--more-->

- 再把vim键位图贴出来：

![](https://farm1.staticflickr.com/798/27532370398_e3dac173f7_o.jpg)

### 进入VIM编辑模式

快捷键| 解释
---|---
i |在当前字符插入
I |在光标所在行的行首插入
a |在光标后插入
A |在光标所在行的末尾插入
o |在当前行后插入新的一行
O |在当前行前插入新的一行

### vim命令模式

快捷键| 解释
---|---
`/word` | 在光标之后查找关键词，如果存在多个匹配项，按n向后搜索
`?word` | 在光标之前查找关键词，如果存在多个匹配项，按n向前搜索
`:1,$s/word1/word2/g` | （难点）将文档中的word1替换为word2，不加g则只替换每行的第一个word1
`:m,ns/word1/word2/g` |（难点）将第m行和第n行之间的word1替换为word2，不加g则只替换每行的第一个word1

#### 打开/保存/退出/改变文件

快捷键| 解释
---|---
`:e [path to file]` | 打开一个文件
`:w` | 存盘
`:saveas [path to file]` | 另存为 <path/to/file>
`:x`、 `ZZ` 、`:wq` | 保存并退出 (`:x `表示仅在需要时保存；`ZZ`不需要输入冒号并回车)
`:X `| 设置密码保存并退出，使用此命令后cat 该文件会显示乱码，再次打开需输入密码
`:q!` | 退出不保存 
`wq!`|强制保存退出
`:qa!` |强行退出所有的正在编辑的文件，就算别的文件有更改。
`:bn `、 `:bp` | 你可以同时打开很多文件，使用这两个命令来切换下一个或上一个文件。

### vim使用常见问题

#### 两个小窍门

* `vim +10 [filename]` ，即进入会光标自动跳转到第10行

* `bash` 命令 `!$` 自动重复上一条命令。

#### 如何解决代码自动缩进造成乱码

##### 解决思路：

* 方法一：命令模式，输入 `:set noai nosi `

* 方法二：paste模式

```
:set paste 
:set nopaste
```

#### putty, xshell连接linux，小键盘不能输入数字

##### 解决思路：

* 在`putty`上，选项`Terminal`->`Features`里，找到`Disable application keypad mode`，选中即可，

* 在xshell上，修改`session 属性` -> `终端`->`VT模式`->`初始数字键盘模式`。


### 延伸阅读

* [vim的特殊用法](http://www.apelearn.com/bbs/thread-9334-1-1.html) 

* [vim常用快捷键总结](http://www.apelearn.com/bbs/thread-407-1-1.html) 

* [vim快速删除一段字符](http://www.apelearn.com/bbs/thread-842-1-1.html) 



