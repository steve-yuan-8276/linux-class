---
title: 'python爬虫学习笔记：Requests 的基本用法'
date: 2017-12-18 13:17:47
categories: python 
tags: [python, scrapy,  requests] 
---

### 安装

```
pip3 install requests
```

### Requests的简单用法

#### Requests库的七个主要方法

| 序号 | 方法 | 说明 |
| --- | --- | --- |
| 1 | requests.request() | 构造一个请求，支撑以下各方法的基础方法 |
| 2 | requests.get()  | 获取HTML网页的主要方法，对应于HTTP的GET |
| 3 | requests.head()  | 获取HTML网页头信息的方法，对应于HTTP的HEAD |
| 4 | requests.post()  | 向HTML网页提交POST请求的方法，对应于HTTP的POST |
| 5 | requests.put() | 向HTML网页提交PUT请求的方法，对应于HTTP的PUT  |
| 6 | requests.patch()  | 向HTML网页提交局部修改请求，对应于HTTP的PATCH |
| 7 | requests.delete()  | 向HTML网页提交删除请求，对应于HTTP的DELETE  |

<!--more-->

##### Requests.get的用法：

```
import requests       #导入Requests库
r = requests.get(url)       #使用get方法发送请求，返回包含网页数据的Response并存储到Response对象r中
```

##### Response对象的属性：

* r.status_code：http请求的返回状态，200表示连接成功(阅读HTTP状态码，了解各状态码含义)
* r.text：返回对象的文本内容
* r.content：猜测返回对象的二进制形式
* r.encoding：分析返回对象的编码方式
* r.apparent_encoding：响应内容编码方式（备选编码方式）


### 爬取网页通用框架


```
#定义函数
def getHTMLText(url):
    try:
        r = requests.get(url,timeout=20) #设置超时
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except: #异常处理
        return "产生异常"

if __name__ == '__main__':
    url = " "
    print(getHTMLText(url)) #调用函数
```


### 爬虫协议

爬虫协议，也被叫做robots协议，是为了告诉网络蜘蛛哪些页面可以抓取，哪些页面不能抓取

#### 如何查看爬虫协议：
在访问网站域名后加上robots.txt即可，例如查看百度网站的爬虫协议：https://www.baidu.com/robots.txt

#### 爬虫协议属性： 
##### 拦截所有的机器人： 

```
User-agent: * 
Disallow: /
```

##### 允许所有的机器人： 

```
User-agent: * 
Disallow:
```


