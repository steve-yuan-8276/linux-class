---
title: 前端学习笔记：制作响应式网页的利器Bootstrap 基础
date: 2017-12-22 16:47:32
categories: front-end
tags: [front-end, bootstrap] 
---

### 写在前面

[Bootstrap 中文站点](http://v3.bootcss.com/)

本笔记是对于Bootstrap v3 主要内容的整理。

### BootstrapCDN

```
<!-- 新 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css">
<!-- 可选的Bootstrap主题文件（一般不用引入） -->
<link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>

```
<!--more-->

### 布局容器
为了使用bootstrap大名鼎鼎的格栅系统，需要在body里面新建div，设置一个容器，把所有的内容都包裹在里面。
容器的样式（class）有两种：

```
.container
.container-fluid
```

**WARNING：**由于 padding 等属性的原因，下面容器类不能互相嵌套。

#### .container 类用于固定宽度并支持响应式布局的容器。

```
<div class="container">
  ...
</div>

```
#### .container-fluid 类用于 100% 宽度，占据全部视口（viewport）的容器。

```
<div class="container-fluid">
  ...
</div>
```

### 格栅系统

bootstrap 把每一行称之为“row”，每一列称为“col” ，默认最大有**12列**，所有“列（column）必须放在 ” .row 内。

根据屏幕大小的不同，预设了一些参数，具体如下：

#### 格栅系统参数
屏幕尺寸/参数 | 手机 | 平板 | 桌面显示器 | 超大显示器
---|---|---|---|---
前缀|	.col-xs-	|.col-sm-|.col-md-|.col-lg-
.container 最大宽度| 自动|750px|970px	|1170px
列（column）数|	12|12|12|12
最大列（column）宽|自动|~62px|~81px|~97px
槽（gutter）宽	|30px （每列左右均有 15px）|同上|同上|同上

#### 列偏移
使用 .col-md-offset-* 类可以将列向右侧偏移，实际相当于增加左侧的边距，后面的星号表示列，比如“col-md-offset-2”表示右移2列。

#### 示例
```
<div class="row">
  <div class="col-xs-12 col-sm-6 col-md-8">.col-xs-12 .col-sm-6 .col-md-8</div>
  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
</div>
<div class="row">
  <div class="col-xs-6 col-sm-4">.col-xs-6 .col-sm-4</div>
  <div class="col-xs-6 col-sm-4">.col-xs-6 .col-sm-4</div>
  <!-- Optional: clear the XS cols if their content doesn't match in height -->
  <div class="clearfix visible-xs-block"></div>
  <div class="col-xs-6 col-sm-4">.col-xs-6 .col-sm-4</div>
</div>
```
#### 媒体查询 (Media Query)
在栅格系统中，在 Less 文件中使用以下媒体查询（media query）来创建关键的分界点阈值。

```
/* 超小屏幕（手机，小于 768px） */
/* Bootstrap 是移动设备优先，因此不用设置此类媒体查询 */

/* 小屏幕（平板，大于等于 768px） */
@media (min-width: @screen-sm-min) { ... }

/* 中等屏幕（桌面显示器，大于等于 992px） */
@media (min-width: @screen-md-min) { ... }

/* 大屏幕（大桌面显示器，大于等于 1200px） */
@media (min-width: @screen-lg-min) { ... }

```
偶尔也会在媒体查询代码中包含 max-width 从而将 CSS 的影响限制在更小范围的屏幕大小之内。

```
@media (max-width: @screen-xs-max) { ... }
@media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
@media (min-width: @screen-md-min) and (max-width: @screen-md-max) { ... }
@media (min-width: @screen-lg-min) { ... }

```

### 预定义样式

#### 示例：

![](https://farm5.staticflickr.com/4632/28352837839_6e9f03ccc3_o.png)

#### Code：

```
<!-- Standard button -->
<button type="button" class="btn btn-default">（默认样式）Default</button>

<!-- Provides extra visual weight and identifies the primary action in a set of buttons -->
<button type="button" class="btn btn-primary">（首选项）Primary</button>

<!-- Indicates a successful or positive action -->
<button type="button" class="btn btn-success">（成功）Success</button>

<!-- Contextual button for informational alert messages -->
<button type="button" class="btn btn-info">（一般信息）Info</button>

<!-- Indicates caution should be taken with this action -->
<button type="button" class="btn btn-warning">（警告）Warning</button>

<!-- Indicates a dangerous or potentially negative action -->
<button type="button" class="btn btn-danger">（危险）Danger</button>

<!-- Deemphasize a button by making it look like a link while maintaining button behavior -->
<button type="button" class="btn btn-link">（链接）Link</button>

```

最后，更多更详细的资料参见[官方说明文档](http://v3.bootcss.com/css/#grid-example-mixed-complete)。


