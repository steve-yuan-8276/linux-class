---
title: '前端学习笔记：CSS的时髦用法——BEM'
date: 2017-12-16 20:27:29
categories: front-end 
tags: [feont-end, CSS, BRM] 
---

###  什么是BEM？
BEM是由Yandex团队提出的一种CSS Class 命名方法，旨在更好的创建CSS/Sass模块。

### 菜鸟级BEM代码
```
.aboutSection {
    background-color: tomato;
}

.aboutSection__wrapper {
    max-width: 108rem;
    padding: 3rem 0;
}

.aboutSection__headingContainer {
    background-color steelblue;
    .aboutSection__header {
        font-size: 2.4rem;
        font-weight: 700;
    }
    .aboutSection__subHeader {
        font-size: 1.8rem;
        color: green;
    }
}
```
<!--more-->

###  入门级BEM代码
#### 利用&选择器的BEM创建的Sass模块
**通过使用一组便利的选择器和运算符，我们可以使得BEM出类拔萃。其中最重要的是新加入Sass中的&选择器.在声明内&选择器会直接引用他的父选择器。当嵌套使用'&'时，它会直接抓取父选择器的类名。**

```
.aboutSection {
    &__wrapper {
        max-width 108rem;
        padding: 3rem 0;
    }
    &__headingContainer {
        background-color: steelblue;
    }
    &__header {
        font-size: 2.4rem;
        font-weight: 700;
    }
    &__subHeader {
        font-size: 1.8rem;
        color: green;
    }
}
```

### 进阶BEM代码
**@extend指令能让我们确保所选择的项目也具有nav_item类中的所有样式，从而清除HTML中的nav_item标记。**

```
<nav class="nav">
    <ul class="nav__container">
        <li class="nav__item"></li>
        <li class="nav__item"></li>
        <li class="nav__item"></li>
        <li class="nav__item nav__item--active"></li>
    </ul>
</nav>
```

上述html的CSS文件可以这样写：

```
.nav {
    background-color: steelblue;
    &__container {
        display: flex;
        justify-content: space-between;
    }
    &__item {
        color: white;
        &--active {
            @ extend .nav__item;   //@后面没有空格 
            border-bottom: 1px solid red;
      
    }
}

```

