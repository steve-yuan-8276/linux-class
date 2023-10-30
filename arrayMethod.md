---
title: '前端学习笔记： Array 常用数组方法'
date: 2017-12-27 14:14:15
categories: JavaScript 
tags: [freeCodeCamp, JavaScript, array] 
---

除了array.map() 和 array.filter()，还有一下比较常用的数组方法。

### 数组方法 reduce 

数组方法 reduce 用来迭代一个数组，并且把它累积到一个值中。

使用 reduce 方法时，你要传入一个回调函数，这个回调函数的参数是一个累加器 （比如例子中的 previousVal) 和当前值 (currentVal）。

reduce 方法有一个可选的第二参数，它可以被用来设置累加器的初始值。如果没有在这定义初始值，那么初始值将变成数组中的第一项，而 currentVal 将从数组的第二项开始。

示例：

```
//reduce 来让数组中的所有值相减：
var singleVal = array.reduce(function(previousVal, currentVal) {
  return previousVal - currentVal;
}, 0);

//reduce 来让数组中的所有值相加：
var singleVal = array.reduce(function(previousVal, currentVal) {
  return previousVal + currentVal;
}, 0);

```
<!--more-->

###   数组方法 sort
使用 sort 方法，你可以很容易的按字母顺序或数字顺序对数组中的元素进行排序。

与我们之前用的数组方法仅仅返回一个新数组不同， sort 方法将改变原数组，返回被排序后的数组。

sort 可以把比较函数作为参数传入。比较函数有返回值，当 a 小于 b，返回一个负数；当 a 大于 b ，返回一个正数；相等时返回0。

如果没有传入比较函数，它将把值全部转成字符串，并按照字母顺序进行排序。

```
//把元素按照从小到大的顺序进行排列：
var array = [1, 12, 21, 2];
array.sort(function(a, b) {
  return a - b;
});

//把元素按照从大到小的顺序进行排列：
var array = [1, 12, 21, 2];
array.sort(function(a, b) {
  return b - a;
});
```

#### 数组方法 reverse

你可以使用 reverse 方法来翻转数组。

```
//使用 reverse 来翻转 array 数组，并赋值给 newArray

var myArray = [1, 2, 3];
myArray.reverse();    //输出： [3, 2, 1]
```

#### 数组方法 ：concat

concat 方法可以用来把两个数组的内容合并到一个数组中。

concat 方法的参数应该是一个数组。参数中的数组会拼接在原数组的后面，并作为一个新数组返回。

示例

```
//使用 .concat() 将 concatMe 拼接到 oldArray 后面，并且赋值给 newArray。
var oldArray = [1,2,3];
var newArray = [];
var concatMe = [4,5,6];
newArray = oldArray.concat(concatMe);
```

#### 数组方法： split
使用 split 方法按指定分隔符将字符串分割为数组。

你要给 split 方法传递一个参数，这个参数将会作为一个分隔符。

示例：

```
//使用 split 方法来把字符串 string 分割为数组 array

var string = "Split me into an array";
var array = [];
array = string.split(' ');

```
#### 数组方法 ：join

使用 join 方法来把数组转换成字符串，里面的每一个元素可以用你指定的连接符来连接起来，这个连接符就是你要传入的参数。

示例：

```
//使用 join 方法，连接符为' '把数组 joinMe 转化成字符串 joinedString

var joinMe = ["Split","me","into","an","array"];
var joinedString = '';
joinedString = joinMe.join(' ');
```

