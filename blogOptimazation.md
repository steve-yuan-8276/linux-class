---
title: 'HEXO折腾笔记：给自己的博客做个美容'
date: 2018-01-23 22:44:27
categories: hexo 
tags: [hexo, github, next] 
---

### 写在前面

其实早就想折腾，一来忙着换工作，没心情；二来水平很菜，怕一不小心搞坏了。不过就想很多事情一样，心急吃不热豆腐。有些事情还是急不来的，所以……让我coding一下，冷静冷静。

是的，学习，让我们感觉到很踏实。

### fork me on github

#### 1）通过[github-ribbons](https://github.com/blog/273-github-ribbons)或[github-corners](http://tholman.com/github-corners/)，寻找合适的图标，把代码复制下来。**注：红框位置替换为自己的github地址**

![](https://farm1.staticflickr.com/816/41228017641_01dce154d1_o.png)

#### 2）打开` themes/next/layout/_layout.swig `文件，把代码复制到`<div class="headband"></div>`下面。

<!--more-->

### 静态网页压缩

#### 1）在站点的根目录下执行以下命令：

```
$ npm install --global gulp-cli   //全局安装
$ npm install --save-dev gulp   //npm install --save-dev gulp
$ touch gulpfile.js  //新建 gulpfile.js
```

**注：**[gulp.js 官方使用说明](https://github.com/gulpjs/gulp/blob/v3.9.1/docs/getting-started.md)

#### 2）采用atom 打开gulpfile.js，复制粘贴以下代码：

```
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
// 压缩 public 目录 css
gulp.task('minify-css', function() {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss())
        .pipe(gulp.dest('./public'));
});
// 压缩 public 目录 html
gulp.task('minify-html', function() {
  return gulp.src('./public/**/*.html')
    .pipe(htmlclean())
    .pipe(htmlmin({
         removeComments: true,
         minifyJS: true,
         minifyCSS: true,
         minifyURLs: true,
    }))
    .pipe(gulp.dest('./public'))
});
// 压缩 public/js 目录 js
gulp.task('minify-js', function() {
    return gulp.src('./public/**/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('./public'));
});
// 执行 gulp 命令时执行的任务
gulp.task('default', [
    'minify-html','minify-css','minify-js'
]);
```
3）更新推送

```
hexo g
gulp
hexo d

```

### 增加字数统计

利用统计功能，显示文章字数统计,阅读时长,总字数

1）在站点的根目录下，输入命令：

```
$ npm i --save hexo-wordcount
```

2）打开 `themes/next/_config.yml `，搜索关键字 `post_wordcount`：

```
# Post wordcount display settings
# Dependencies: https://github.com/willin/hexo-wordcount
post_wordcount:
  item_text: true
  #字数统计
  wordcount: true
  #预览时间
  min2read: true
  #总字数,显示在页面底部
  totalcount: true
  separated_meta: true
```

### 实现动态背景

这个比较酷炫，但是实现方法是最简单的，因为next V5.1.1主题自带，大牛已经写好了只要在设置文件中开启即可。

在站点目录，找到next主题文件夹，路径是`themes/next/_config.yml`，搜索找到`canvas_nest: false`，把它改为`canvas_nest: true` ，齐活。

### 预览效果
最后的最后，所有的这些改动，会让blog看起来挺像那么回事。虽然也还是菜鸟，不过，怎么说呢……呵呵



