---
title: 'Python笔记： python 爬虫基础'
date: 2017-12-17 17:54:52
categories: python 
tags: [python, scrapy, web] 
---
### 环境搭建
* [python3](https://www.python.org/)

* google chrome

* 编辑器（sublime、pyCharm、atom）
 
### 爬虫三步走

#### 第一步：使用requests获得数据： 
1. 导入requests 
2. 使用requests.get获取网页源码

```
import requests      
r = requests.get('https://book.douban.com/subject/1084336/comments/').text
```
<!--more-->

#### 第二步：使用BeautifulSoup4解析数据： 
1. 导入bs4 
2. 解析网页数据 
3. 寻找数据 
4. for循环打印

```
from bs4 import BeautifulSoup
soup = BeautifulSoup(r,'lxml')
pattern = soup.find_all('p','comment-content')
for item in pattern:
    print(item.string)

```
#### 第三步：使用pandas保存数据： 
1. 导入pandas 
2. 新建list对象 
3. 使用to_csv写入

```
import pandas
comments = []
for item in pattern:
    comments.append(item.string)    
df = pandas.DataFrame(comments)
df.to_csv('comments.csv')

``` 
### 爬虫示例

#### 使用urllib包获取百度首页信息：

```
import urllib.request
#导入urllib.request

f = urllib.request.urlopen('http://www.baidu.com/')
#打开网址，返回一个类文件对象

f.read(500)
#打印前500字符

f.read(500).decode('utf-8')
#打印前500字符并修改编码为utf-8
```

Requests包：由于requests是python的第三方库，阅读[快速上手requests](http://docs.python-requests.org/zh_CN/latest/user/quickstart.html)



#### 使用Requests库获取百度首页信息：

```
import requests      #导入requests库

r = requests.get('https://www.baidu.com/')
#使用requests.get方法获取网页信息

r
r.text      #打印结果

r.encoding='utf-8’       #修改编码

r.text        #打印结果
```


