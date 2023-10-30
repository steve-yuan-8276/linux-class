---
title: 'Mac学习笔记：终端命令行都能做哪些有趣的事情'
date: 2018-01-30 21:42:39
categories: shell
tags: [shell, bash, commandLine] 
---

### 写在前面

最近在学习的过程中，发现一些蛮有趣的shell trick，干脆整理一下，免得忘记了。

#### tree

list  contents  of directories in a tree-like format.顾名思义，就是可以像树状结构一样查看文件的目录。如下图所示：

<!--more-->

![](https://farm5.staticflickr.com/4604/39234422945_1ce3d988ce_o.jpg)

##### Installation

```
brew install tree //需要先安装brew
```
##### Verify if the installation was successful:

```
tree --version
```
##### How to Use:

```
tree -L 2 //L 表示level，后面的2表示两级目录
```

最有用的就是这个，更多内容可以输入 `man tree` 或者 `tree -help` 进行查询。

#### 命令行查询天气

http://wttr.in 是一个允许你搜索世界各地天气预报的网站，而且它的是以 ASCII 字符的形式来显示结果的。通过使用 cURL 访问 http://wttr.in，就能直接在终端显示查询结果了。

查询效果是这样的，还是挺骚包的
![](https://farm5.staticflickr.com/4711/39421680974_105b070e0e_o.jpg)

##### 获取指定地点的天气

###### 方式1:直接输入地点

```
$ curl wttr.in/New+York

$ curl wttr.in/New_York

$ curl wttr.in/newyork
```
**warning： 教程上说单词之间要用+或者下划线连接，实测直接连在一起也可以，但是不能有空格，会报错。**

###### 方式2:获取地标性建筑天气

```
curl wttr.in/~tiananmen-square
```
![](https://farm5.staticflickr.com/4714/39234428045_bb0d6db5e7_o.jpg)

###### 方式3: 获取ip所在地天气

```
$ curl wttr.in/@linuxconfig.org
```
##### TIPS

默认情况下，wttr.in 会根据你的实际地址来决定显示哪种温度单位（C 还是 F）。基本上，在美国，使用的是华氏度，而其他地方显示的是摄氏度。

当然，也可以指定显示的温度单位，在 URL 后添加 ?u 会显示华氏度，而添加 ?m 会显示摄氏度。

```
$ curl wttr.in/New_York?m    //强制显示摄氏度

$ curl wttr.in/Toronto?u     //强制显示华氏度
```

### 查询日历时间

```
$ cal     //查看当月
$ cal 2017      //查看当年的日历
$ date     查询具体日期时间
```

### 查询比特币价格

![](https://farm5.staticflickr.com/4628/28352847519_4669b0281e_o.png)

Installation：
前提是已经安装了node.js 和 npm，之后运行以下命令行：

```
 npm install -g coinmon
```
How to use：

```
coinmon     //不带任何参数运行 Coinmon，显示前 10 位加密货币

coinmon -t 20     //使用 -t 标志查看最高的 n 位加密货币

coinmon -c cny    //价格默认以美元显示。你还可以使用 -c 标志将价格从美元转换为另一种货币；cny表示人民币

coinmon -f btc     //使用-f 加货币简称查询制定货币价格

coinmon -h     //查询说明文档
```




