---
title: HEXO NEXT设置社交图标social icon遇到的坑
date: 2017-12-15 09:41:14
categories: hexo
tags: [front-end, hexo, icon] 
---

### 写在前面

一定要学会阅读说明文档！
一定要学会阅读说明文档！
一定要学会阅读说明文档！
重要的事情说三遍！

### 这是一个神马坑呢？
作为一个有志于从事前端/全栈开发的青年，怎么可以没有blog呢？于是，果断按照网上教程照猫画虎做了一个，却在icon设置上栽了跟头。

<!--more-->
具体是这样的：

按照[说明文档](http://theme-next.iissnan.com/theme-settings.html)和[isuues](https://github.com/iissnan/hexo-theme-next/wiki/%E8%AE%BE%E7%BD%AE%E4%BE%A7%E8%BE%B9%E6%A0%8F%E7%A4%BE%E4%BA%A4%E9%93%BE%E6%8E%A5)提示的操作方法，在[fontawesome](http://fontawesome.io/icons/)找到相应的图片名称，但是显示结果很预期的不一样。

实际显示效果是这样的（如图），只有facebook正常，其他三个图标都不对，不知道为什么。
![](https://farm5.staticflickr.com/4612/26259645308_a69050b733_o.jpg)

### 问题分析解决
你说怪不怪，为啥只有FB的icon正确显示，其他的不见踪迹呢？难道我只有在Facebook比较受欢迎，其他的都不受待见？
![](https://farm5.staticflickr.com/4770/40133549311_53b642c90c_o.jpg)
试着更换了别的图标，还是不对！

神马情况？直到我看到了这句话：
![](https://farm5.staticflickr.com/4612/39421700664_6abe018cd8_o.jpg)

示例代码：
![](https://farm5.staticflickr.com/4748/40133551101_f8e2cb015f_o.jpg)

看到这里，突然有一种想抽自己的冲动。原来答案一直就杵在眼前，而我却一直视而不见，还花了三天的时间各种查资料！

当然，这个social_icons: 也很有迷惑性，之前总在这里改来改去，其实该修改的在前面social目录下就已经修改完了，这里只要保持光溜溜就好。

好吧！此处省略一万字感慨，今天的埋坑记录就到这里。





