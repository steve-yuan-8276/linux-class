---
title: 'Github折腾笔记：step by step 利用github.io建立个人博客'
date: 2017-12-12 16:08:28
categories: hexo 
tags: [hexo,github] 
---



### 折腾前提：
1. 了解git基础知识，最好有github账户（没有的话，需要新建一个）。
2. 了解并掌握MarkDown基本语法
3.  已安装[node.js](https://nodejs.org/en/)

### 第一步：本地安装HEXO
#### 1.命令行输入：

```
npm install -g hexo
```
#### 2.新建一个blog文件夹，然后通过终端进入该文件夹

```
cd documents
touch blog  
cd blog
```

#### 3.执行以下命令：

```
hexo init
```
#### 4.安装依赖包

```
npm install
```

到这里，hexo安装完毕。
<!--more-->
### 第二步：新建github.io

**WARNING: 对于已经配置好github账户的你，请跳过本章节，直接进入下一步。**

#### 1.新建github账户

#### 2.生成SSH密钥

```
ssh-keygen -t rsa -C XXX@gmail.com  //此邮箱为github注册邮箱
```
一路下一步，会得到两个id_rsa和id_rsa.pub两个文件，其中id_rsa是私钥，自己偷偷看管好就行；id_rsa.pub是公钥，使用cat命令查看一下内容，复制出来，下面会用到。

#### 3.打开[github设置页面](https://github.com/settings/ssh)，点击New SSH key,然后粘贴id_rsa.pub内容，然后点击Add SSH key。

#### 4. 新建github.io，方法参见[官方文档](https://pages.github.com/)

### 第三部：设置HEXO

#### 1.在hexo文件夹内找到_config.yml配置文件，sublime打开编辑：

```
# Hexo Configuration  
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site 站点信息设置
title:  #站名
subtitle:  #副标题
description: #站描述
author:  #作者
language: zh-CN #语言
timezone:

# URL 链接设置
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://blog.prozin.xyz
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory 文件目录
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing 文章
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format 日期
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination 分页
## Set per_page to 0 to disable pagination
per_page: 20
pagination_dir: page

# Extensions 扩展
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape #默认主题

# Deployment 这里设置了Git获
#这里一定要注意不要写错了，否则部署到Github上会出问题
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: 仓库地址.git    
  branch: master
  message: '站点更新:{{now("YYYY-MM-DD HH/mm/ss")}}'

```

**划重点：**
1. 配置文件的冒号“:”后面有一个空格
2. repo: 就是github地址.git ，这里的“.git”一定不能忘。

#### 2.生成网页

```
hexo generate
```
#### 3. 部署.deploy目录

```
hexo deploy
```
**WARNING：这里有个坑**

我进行到这一步的时候遇到了错误提示：ERROR Deployer not found: github,  google了一下，找到了这个[文档](https://github.com/hexojs/hexo/issues/1040),  给出的填坑方法如下：

```
npm install hexo-deployer-git --save
```
安装完成后，再运行deploy命令，顺利通过，进入下一关。

#### hexo 常用命令行
序号|命令|含义
---|---|---
1|hexo help| #查看帮助
2|hexo init| #初始化一个目录
3|hexo new| "postName" #新建文章
4|hexo new page "pageName" |#新建页面
5|hexo generate| #生成网页，可以在 public 目录查看整个网站的文件
6|hexo server| #本地预览，'Ctrl+C'关闭
7|hexo deploy| #部署.deploy目录
8|hexo clean| #清除缓存，**强烈建议每次执行命令前先清理缓存，每次部署前先删除 .deploy 文件夹**


### 第四步：更改主题为NEXT

具体参见[说明文档](http://theme-next.iissnan.com/getting-started.html) 内容详细到令人发指，step by step就好了。

### 第五步：修建并发布文章

1.新建文档

```
hexo new "标题"

```
2.编辑文章
刚刚生成的文档在在sourse/ _posts文件夹下生，默认是 .md文件，推荐使用Mweb进行编辑。

3.发布文章

```
hexo clean
hexo generate
hexo deploy
```
以下提示说明部署成功

```
[info] Deploy done: git
```
然后就齐活了，访问你自己的github.io 地址，就可以看到博客了。

### 参考文档：

1.  [手把手教你建github技术博客](http://www.jianshu.com/p/701b1095da11)

2.  [Mac OS使用github+hexo搭建自己的博客](http://www.jianshu.com/p/ee587a8104cf)

3. [CentOS下搭建Hexo + github 博客](http://www.jianshu.com/p/0823e387c019)

4.  [5分钟 搭建免费个人博客](http://www.jianshu.com/p/4eaddcbe4d12)

5. [手把手教你使用Hexo + Github Pages搭建个人独立博客](https://div.io/topic/1691)

6. [Github Pages说明文档](http://gitbeijing.com/pages.html)




