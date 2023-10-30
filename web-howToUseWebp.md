---
title: '前端学习笔记：如何使用Google官方推荐的webp进行图片压缩'
date: 2017-12-12 18:27:49
categories: front-end 
tags: [web,front-end,google] 
---

###  第一步：安装
#### 1. 安装Xcode，App Store直接下载安装
#### 2. 安装macport
下载地址在[这里](https://distfiles.macports.org/MacPorts/)

**WARNING:)**
- 注意选择支持本机的安装包，因为我的系统是machighSierra，所以选择MacPorts-2.4.2-10.13-HighSierra.pkg

- 打开终端，输入 port，正确显示版本号说明安装成功。

- Update MacPorts： 用以下命令行sudo port selfupdate

<!--more-->
#### 3. Install libwebp:
```
 sudo port install webp
```

###  第二步：使用
####  Images to the WebP Format
```
cwebp -q 80 image.png -o image.webp
```

More  instruction, check [ducumentation](https://developers.google.com/speed/webp/docs/cwebp)

#### WebP Format to Images
```
dwebp image.webp -o image.png
```
More  instruction, check [ducumentation](https://developers.google.com/speed/webp/docs/dwebp)



