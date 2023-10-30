---
title: '为GitHub Pages自定义域名配置HTTPS'
date: 2017-12-15 17:51:19
categories: hexo 
tags: [hexo, github, https] 
---

原帖地址在[这里](https://www.thinktxt.com/jekyll/2017/02/06/jekyll-series-github-pages-custom-domain-https.html)，以下是笔记整理：

**WARNING:  亲测，使用此方法大概过了整整一天（24小时）才给网站加上那把绿色小锁。所以，每一步做对了也不见得就能看到立竿见影的效果，还需要耐心等待。**
 
### 前提准备
搭建完成github.io
购买域名
注册[Cloudflare](https://www.cloudflare.com/)账号

### 基本思路

1. 在 Cloudflare中添加你的域名，设置2个A记录；
2. 去你的域名注册商更改 DNS Server 成 Cloudflare 所提供的；
3. 在 Cloudflare 中配置 Crypto ，选择 Full （这一步要一段时间才能起效）
4. 在 Cloudflare 配置 Page Rules ，一个是 always use https ，一个是 redirect http 到 https，保持等待生效。

<!--more-->

### 具体操作：

#### step 1 ：添加域名，设置解析记录
登录Cloudflare后，在这里 添加 你的域名，如下图，输入你的域名，例如 thinktxt.com 并点击Begin Scan开始扫描；
![](https://farm5.staticflickr.com/4756/28352857899_d4f4bd9538_o.jpg)

完成域名解析扫描后，点击 Continue Setup 继续；

看到DNS记录（包括子域）列表之后，按照下图提示设置后，点击 Continue，进入下一步
![](https://farm5.staticflickr.com/4710/39234442775_599ef25c74_o.jpg)
**注：此处设置cname是为了重定向www**

选择免费服务计划，然后点击下一步

#### step 2 ：更改DNS Server
以万网为例，在域名管理中选择DNS修改/创建，如下图：
![](https://farm5.staticflickr.com/4742/28352858839_150edce8cc_o.jpg)

在万网修改好DNS之后，在Cloudflare点击继续，如下图：

![](https://farm5.staticflickr.com/4672/39421705074_b1f6d8898f_o.jpg)

注：官方说域名服务器修改最长需要72小时生效，显示 Status: Active 即可。

重定向：点击 Page Rules => Create Page Rule

添加顶级域名重定向到https
![](https://farm5.staticflickr.com/4677/40100631212_758ea4a923_o.jpg)


强制使用SSL，可以使用通配符*
![](https://farm5.staticflickr.com/4741/39421707014_8a2750321f_o.jpg)


完成，剩下的就是等待了，正常情况下过个把小时就能以https访问了。



