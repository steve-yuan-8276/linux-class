---
title: 'python爬虫学习笔记：Xpath的基本用法'
date: 2017-12-20 21:56:58
categories: python 
tags: [python, scrapy,  Xpath] 
---

### installation

```
pip install lxml
```

### Xpath的使用：

#### 1. 使用Xpath解析网页数据的步骤

* 从lxml导入etree
* 解析数据，返回xml结构
* 使用.xpath()寻找和定位数据


```
from lxml import etree

html ='''#省略'''#html数据，使用requests获取

s = etree.HTML(html)#解析html数据

print(s.xpath())#使用.xpath()

```
<!--more-->

#### 2. 获取Xpath的方法

##### 第一种方法：从浏览器直接复制
* 首先在浏览器上定位到需要爬取的数据
* 右键，点击“检查”，在“Elements”下找到定位到所需数据
* 右键——Copy——Copy Xpath，即可完成Xpath的复制

示例：
![](https://farm5.staticflickr.com/4619/40100626262_150daf64f4_o.jpg)

```
#从浏览器直接复制Xpath
import requests
from lxml import etree

url = 'https://book.douban.com/subject/1084336/comments/'
r = requests.get(url).text

s = etree.HTML(r)
print(s.xpath('//*[@id=“comments”]/ul/li[1]/div[2]/p/text()'))
```
##### 第二种方法：手写Xpath
* 获取文本内容用 text()
* 获取注释用 comment()
* 获取其它任何属性用@xx，如： href, src, value
* 想要获取某个标签下所有的文本（包括子标签下的文本），使用string: 如”< p>123< a>来获取我啊< /a>< /p>”，这边如果想要得到的文本为”123来获取我啊”，则需要使用string
* starts-with 匹配字符串前面相等
* contains 匹配任何位置相等
示例：
![](https://farm5.staticflickr.com/4719/39234434515_428623e27d_o.jpg)

```
#手写Xpath
import requests
from lxml import etree

url = 'https://book.douban.com/subject/1084336/comments/'
r = requests.get(url).text

s = etree.HTML(r)
print(s.xpath('//div[@class="comment"]/p/text()')[0])

```

