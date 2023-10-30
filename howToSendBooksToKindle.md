---
title: 'HowTo:如何优雅滴将电子书同步到kindle'
date: 2018-04-16 08:59:02
categories: kindle
tags: [dropbox, IFTTT, kinlde, notes] 
---

### 写在前面

书籍是人类进步的阶梯！这则笔记主要整理如何把书籍发送到Kindle的三个技巧。

### 入门：利用邮箱发送到kindle 

第一步：登陆个人的亚马逊kindle账户（没有的话需要注册一个），点击`管理我的内容和设备`

![](https://farm1.staticflickr.com/793/41443427342_a53c279219_o.png)

 <!--more-->

点击‘设置’

![](https://farm1.staticflickr.com/797/40592248735_b5d7391c12_o.png)

页面往下拉，电子书自动更新这里，选择 `已启用`

![](https://farm1.staticflickr.com/793/39677205650_dfec82e017_o.png)

在个人文档设置 这里的电子邮箱，指的是kindle设备的专属邮箱，你的每个设备对应一个kindle专属邮箱（Kindle账户登陆了几个设备（电脑客户算、手机客户端、Kindle电子书都算），就有几个专属邮箱；其实个人数据库是一样的，只不过推送到专属邮箱到电子书会在自动下载，而在其他的设备上需要手动点击下载而已）

![](https://farm1.staticflickr.com/887/40592335795_7c682c2b1a_o.png)

添加个人` 信任邮箱` （注：信任邮箱指的是你打算通过哪个邮箱发到kindle账户），只有添加了信任邮箱，亚马逊才允许通过该邮箱向kindle发送电子书；官方要求不能超过20个信任邮箱，不过一般人应该也没有那么多个人邮箱。

![](https://farm1.staticflickr.com/822/27614111148_f949bf3aa0_o.png)

添加完成，理论上就可以用邮箱发送电子书了。发送成功之后，亚马逊会自动帮你推送到指定专属设备上。

**注意：**

1. kindle官方支持mobi，你也可以把 `.doc`、 `.txt` 发过去，会自动转成mobi格式的文件推送到设备上。不过个人体验最好还是mobi，看起来很舒服。

2. 邮箱一次性发送文件的个数**不能超过25个。**

3. mobi电子书体积不能超过50兆，否则还是失败。


### 进阶：利用`IFTTT`将`dropbox`存储的电子书发到`kindle`

这里涉及到了3个服务，特别介绍一下：

* [Dropbox：](https://www.dropbox.com/)个人存储的神器，墙内不可用，需要科学上网。

* [IFTTT：](https://ifttt.com/)if this then that，顾名思义，就是让不会编程的人也能享受自动化的乐趣。墙内可用，不过支持的服务大多是像`google`、`dropbox`、`spotify`、`newyork times`等等这种404网站（说到这里脑子里突然涌现出两个成语：井底之蛙、一叶障目）

* gmail：个人用过最好用的邮箱，天朝同样404……

言归正传，如果你已经按照前面教程进行了邮箱设置，这里就很简单了。IFTTT既可以在电脑上操作，也有手机app，这里以电脑端为例：

第一步：注册`IFTTT`账号，假设你有`google`或者`facebook`账户，可以直接用这两个账号授权登陆。

第二步：搜索 kindle，就是红圈标出的这个，然后点击

![](https://farm1.staticflickr.com/816/41444062322_a3a2527bc3_o.png)

第三步：点击 `turn on`,会自动在你的`dropbox`文件夹常见一个叫做`SentToKindle` 的文件夹（前提是你已经授权ifttt访问dropbox）；下面的邮箱地址填的是kindle设备的专属邮箱（这里也有个隐形前提，就是你已经授权ifttt访问google邮箱）。

![](https://farm1.staticflickr.com/900/26614779627_494d3eef4f_o.png)


然后就没有什么啦，其余的选择保持默认就好了。设置完成后，随便拖一个mobi文件到`SentToKindle`，网络没问题的话过几分钟就自动推送到kindle设备了。

**注意：**

1. 如果遇到使用不了的情况，很有可能是授权问题，取消gmail邮箱授权，重新操作一次。

2. 这个服务涉及到好几个授权，个人也不太确定这样做有没有隐私泄漏风险，酌情考虑使用吧。

### 再高级一点：使用`calibre `管理个人电子书

calibre 是个开源免费软件，重点是真的免费！，而且支持这么多系统：

![](https://farm1.staticflickr.com/876/40592933735_8eda24ee67_o.png)

进入[calibre官网](https://calibre-ebook.com/download)，选择相应的版本下载安装。在安装时候，会提示你可以提供个人邮箱推送的服务。实验了一下，发现最方便的还是添加google邮箱。

如果一开始犹豫不决没有添加的话，也可以在这里进行添加。注意确保该邮箱已经添加到亚马逊官网 Kindle 管理后台的 `【设置】`页面中的“已认可的发件人电子邮箱列表”中。

![](https://farm1.staticflickr.com/881/27614880918_0de7c3146a_o.png)

这里还是推荐gmail邮箱，如果是其他邮箱的话还要设置服务和端口信息：

常见邮箱服务器设置：

邮箱服务商 |主机名|端口|加密类型
---|---|---|---
163 邮箱|smtp.163.com| 465 或 587|SSL
126 邮箱|smtp.126.com | 465 或 587|SSL
新浪邮箱| smtp.sina.com|465|SSL
Gmail|smtp.gmail.com | 587|TLS
Hotmail/Live/Outlook邮箱|smtp-mail.outlook.com | 587|TLS

设置完成之后，可以测试一下，没问题就可以正常使用了。打个比方，比如在网上下载了一个epub格式的电子书，导入calibre之后转化为mobi格式，之后右键选择 `conect/share `选择邮箱发至kindle专属邮箱即可。**注意，此处不要点错了，是`conect/share `！**

最后，在多说一句，其实calibre 更强大的功能在于书籍管理，有兴趣可以研究一下。



