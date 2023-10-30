---
title: Github折腾笔记：hexo+github  实现域名绑定
date: 2017-12-13 11:50:04
categories: hexo 
tags: [hexo,github] 
---

### 前提条件：
* 拥有github.io
* 了解bash基本操作
* 花一点点小钱

### step 1 ：购买域名

在[万网](https://wanwang.aliyun.com/)注册账户，实名认证，购买域名，一路按照流程走就OK了。

<!--more-->
### step 2: 域名设置解析

#### 1.登陆[万网](https://wanwang.aliyun.com/)，点击控制台 => 产品与服务 => 域名 => 解析
![](https://farm5.staticflickr.com/4747/40133558121_38db1f3428_o.jpg)


#### 2. 继续点击 添加解析 
![](https://farm5.staticflickr.com/4608/40133558361_989156f7e9_o.jpg)

如图所示，这里只需要改动两处：
一是主机记录，写 “www”
二是记录值，就是github.io的ip地址。获取这个ip值，可以直接打开终端通过ping命令获取。

添加之后选择保存即可。

### step3. 配置hexo

在本地hexo文件根目录下找到source，进入这个目录后创建文件名为 CNAME 的文件，打开之后编辑文件写入刚买来的域名。

```
终端输入：
cd /Users/steveyuan/documents/github-file/steveBlog/source       #这里是文件路径，需要根据实际情况进行调整

touch CNAME       #该文件不写后缀

之后双击打开文档进行编辑，输入域名，保存

www.steve-yuan.com      #这里是自己的域名
```

### step 4: 修复Bug
如果正确进行了以上几步，将会看到以下画面：
![](https://farm5.staticflickr.com/4657/39421710264_7d56cab0e1_o.jpg)

google了一下，原来已经有人踩过这个坑了。虽然解析到了github服务器，但是并没有指向正确的页面。

**解决方法：**

##### 1.进入自己的github.io 的Repository，点击 Create new file，创建一个文件，名字还是叫 CNAME 。

##### 2. 编辑CHAME 文件，内容还是域名，不过不包括“ http：// www ”，点击保存。

##### 3. 进入本地hexo文件夹，重新生成、部署网站。

```
hexo clean
hexo generate 
hexo deploy
```

然后，喝杯茶休息一下，重新在浏览器输入域名，齐活。



