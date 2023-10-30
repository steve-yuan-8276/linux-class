---
title: 'github折腾笔记：如何迁移hexo到新电脑'
date: 2018-01-04 22:06:12
categories: hexo 
tags: [hexo,github] 
---

### 写在前面

2018年1月1日，念叨了好久的新款macbook pro终于获批。开不开心？开心！意不意外？当然意外！

当然，开心之余还伴随着“甜蜜的烦恼”，因为每次安装、配置新电脑，都是一次分心费力的事儿。最重要的，好不容易建立起来的个人博客一定要妥善搬迁过去。尤其是考虑到过去曾经两次把github Repo账户搞的乱七八糟最后被迫删号重建到情况下……

### 前提准备

* [安装git](https://www.steve-yuan.com/2018/01/04/shh-github/)

* [安装node.js](https://nodejs.org/en/)

* [安装hexo](https://nodejs.org/en/)

```
npm install hexo-cli -g
```

<!--more-->

**troubleShooting**
前期准备部分就遭遇trouble到关键原因，当然还是自己太菜了。

这里有个坑，第一次在安装node.js的时候，装的是8.9.4LTS版本，本身没问题，但在安装hexo的时候遇到了错误提示：
![err](https://farm5.staticflickr.com/4603/26259675018_6ebca2d901_o.jpg)
大概就是说权限不够，google了一翻，在stack overflow上找到了答案，具体见[网址](https://stackoverflow.com/questions/16151018/npm-throws-error-without-sudo)

简单说就是要授权，按照网页的提示，在终端中输入：

```
sudo chown -R $USER /usr/local/lib/node_modules
```
### 迁移blog

#### 拷贝文件

这里只需要拷贝以下5个文件/文件夹，其他的文件不用拷，直接忽略即可。

*  config.yml
*  theme
*  scaffolds #文章模板
*  package.json #说明使用哪些包 
*  source

#### 安装模块 

```
cd blog-directory    //  进入blog文件夹 
npm install    //安装模块

```

#### 安装其他组件

```
npm install hexo-deployer-git --save //同步内容至github

npm install hexo -server --save    //server

npm install hexo-generator-feed --save //RSS订阅

npm install hexo-generator-sitemap --save  //站点地图

```
之后，正常情况下应该就可以部署更新blog了。

hexo clean
hexo generate   //可简写为 hexo g
hexo deploy   //可简写为 hexo d

**troubleShooting**
这里也遇到了一个坑，前面配置都没问题，最后在运行 hexo d 的时候，遇到了错误提示：

```
fatal: unable to access 'https://github.com/steve-yuan-8276/steve-yuan-8276.github.io.git/': Failed to connect to github.com port 443: Operation timed out
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: fatal: unable to access 'https://github.com/steve-yuan-8276/steve-yuan-8276.github.io.git/': Failed to connect to github.com port 443: Operation timed out

    at ChildProcess.<anonymous> (/Users/steveyuan/Documents/github-file/steveBlog/node_modules/hexo-util/lib/spawn.js:37:17)
    at emitTwo (events.js:126:13)
    at ChildProcess.emit (events.js:214:7)
    at maybeClose (internal/child_process.js:925:16)
    at Socket.stream.socket.on (internal/child_process.js:346:11)
    at emitOne (events.js:116:13)
    at Socket.emit (events.js:211:7)
    at Pipe._handle.close [as _onclose] (net.js:554:12)

```
后尝试了很久都没有结果，后来ping了一下github.com 发现丢包率100%，不管是走直连、代理（by rules 、global）都不行；诡异的是，chrome浏览器打开github却毫无压力。

无奈之下只好放弃，回到家里，重新又拾了一遍，突然又好了。好吧，大概下午时分人品不行吧。

这一整套下来，不知不觉已经十一点了，今天就到这里吧。


