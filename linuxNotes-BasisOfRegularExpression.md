---
title: 'Linux运维学习笔记：正则表达式基础'
date: 2018-04-19 14:20:07
categories: linux
tags: [linux, regularExpression, notes] 
---

### 写在前面

果然，不管是前端、后端，还是python，统统绕不开正则。这则笔记整理正则表达式的基础知识。

这则笔记是这本书的读书笔记，另有部分内容来自网络。

### 什么是正则表达式 `RegularExpression`？

正则表达式，以下简称‘正则’，英文名`RegularExpression`，是经过专门编写的文本字符串，用来匹配字符串（尤其是文件内字符串）。

正则的历史跟计算机的历史一样古老，不但在vi（vim）、grep及sed等Unix命令行工具中广泛应用，主流的编程语言，比如Python、Perl、Java、JavaScript、C#、Ruby等，都支持正则。

<!--more-->

### 正则基础

#### 常用的正则基本语法

- `[0-9]` `\d`  匹配0-9的任意数字；

![](https://farm1.staticflickr.com/881/27685927128_2429a3c7e7_o.png)

- `\D` 匹配非数字字符，包括一些字符 `-`、 `%`；

![](https://farm1.staticflickr.com/868/41514813902_7a6b3a4d77_o.png)

- `\w` `[a-z]` w小写，匹配任意单词字符

![](https://farm1.staticflickr.com/788/26687219147_d3d84c2ff8_o.png)

- '\W' W大写，匹配所有非单词字符；

![](https://farm1.staticflickr.com/852/27685918678_390f5965fb_o.png)

- `\s` 匹配空白符，包括空格、制表符`\t`、换行符`\n`、回车符`\r`

![](https://farm1.staticflickr.com/859/40844514604_6ac87a7805_o.png)

- `\S` 匹配所有非空白符；

![](https://farm1.staticflickr.com/931/40844524634_b721e5ce69_o.png)

- `. `通配符，可以匹配任意字符；

![](https://farm1.staticflickr.com/857/41555995511_8accbdb282_o.png)

- `{n}` n为数字，表示匹配的对象出现的次数，跟其他的符号配合起来使用

`\w{4}`匹配任意4个连续的字符
![](https://farm1.staticflickr.com/821/39747380600_c029715871_o.png)

- `?` 表示连接词是可选的，比如 -? 表示连接符-可以有，也可以没有；

![](https://farm1.staticflickr.com/907/39747729640_06166784a2_o.png)

- `+` 表示一个或多个；

比如，`.+` 匹配一个或多个任意字符，通常匹配一行内容，不匹配换行符、回车符。

![](https://farm1.staticflickr.com/905/40844727734_95321c2d4f_o.png)

- `*` 表示0个或者多个；

匹配0个或任意多个任意字符

![](https://farm1.staticflickr.com/870/40663069785_25eddd8efd_o.png)

- `\` 脱义符；

比如，`\d` 匹配任意数字；  `\\d` 脱义后，特指`\d`这两个字符

![](https://farm1.staticflickr.com/785/40845041734_0246842479_o.png)

- `|` 选择符，表示从多个可选项中选择一个；

![](https://farm1.staticflickr.com/853/26706808937_5672796ceb_o.png)

-  `\b` 匹配单词的边界；

`\b` 自定边界，但它本身并不匹配任何字符，比如，匹配一个100以内的偶数

![](https://farm1.staticflickr.com/859/41574954341_028686e95d_o.png)

匹配以A开头，后面跟5个字符，以T结尾的单词

![](https://farm1.staticflickr.com/880/40844489374_da0872def2_o.png)

#### 字符组

`[^ ]`字符组取反

- `[\n]`表示匹配换行符； `[\n^]`取反，表示匹配除换行符以外的所有字符，跟 .* 结果一样。

![](https://farm1.staticflickr.com/870/40844705504_dfe7b9a2bb_o.png)

-` [^0-9]` 取反，非数字；

![](https://farm1.staticflickr.com/809/40663391845_3703c0a122_o.png)

- `[^a-z]` 取反，非字母；

![](https://farm1.staticflickr.com/882/41556356501_a151315bfc_o.png)

- `[a-z&&[^e-n]]` 取a-z中间的字母，并将e-n排除

![](https://farm1.staticflickr.com/845/41575070431_1a17448dd7_o.png)


### 延伸阅读

* 《学习正则表达式 》

* 《精通正则表达式（第3版）》

* 《正则表达式经典实例》

