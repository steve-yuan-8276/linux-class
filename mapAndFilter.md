---
title: 前端学习笔记：ES6 中的Array.prototype.map() 和Array.prototype.filter()
date: 2017-12-14 22:42:23
categories: front-end 
tags: [front-end, JavaScript, ES6] 
---

### map()
map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

示例如下：

```
const names = ['Michael', 'Ryan', 'Tyler'];

const nameLengths = names.map( name => name.length );

```
<!--more-->
### filter()
filter() 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。

示例如下：

```
const names = ['Michael', 'Ryan', 'Tyler'];

const shortNames = names.filter( name => name.length < 5 );

```


### map() 和 filter() 连用，一个方法返回的数据可以是另一个方法的新数组。

示例：

```
const names = ['Michael', 'Ryan', 'Tyler'];

const shortNamesLengths = names.filter( name => name.length < 5 ).map( name => name.length );
```

### 练习题：

```
const musicData = [
    { artist: 'Adele', name: '25', sales: 1731000 },
    { artist: 'Drake', name: 'Views', sales: 1608000 },
    { artist: 'Beyonce', name: 'Lemonade', sales: 1554000 },
    { artist: 'Chris Stapleton', name: 'Traveller', sales: 1085000 },
    { artist: 'Pentatonix', name: 'A Pentatonix Christmas', sales: 904000 },
    { artist: 'Original Broadway Cast Recording', 
      name: 'Hamilton: An American Musical', sales: 820000 },
    { artist: 'Twenty One Pilots', name: 'Blurryface', sales: 738000 },
    { artist: 'Prince', name: 'The Very Best of Prince', sales: 668000 },
    { artist: 'Rihanna', name: 'Anti', sales: 603000 },
    { artist: 'Justin Bieber', name: 'Purpose', sales: 554000 }
];
```
//map() 方法创建一个新的数组，并包含以下格式的项目：<album> by <name> sold <sales> copies
```
const albumSalesStrings = musicData.map(album => `${album.name} by ${album.artist} sold ${album.sales} copies`);
console.log(albumSalesStrings);
```

//filter() 方法创建一个新的数组，其中仅包含名称在 10 和 25 个字符之间的专辑。将新数组存储在变量 results 中。

```
const results = musicData.filter(album => album.name.length <= 25 && album.name.length >= 10);
console.log(results);
```
//.map() 和 .filter() 组合到一起
//使用 .filter() 从列表中过滤出销量超过 1,000,000 张的专辑。然后对返回的数组调用 .map()，并创建一个项目格式如下的新数组：

```
<artist> is a great performer
const popular = musicData
  .filter(album => album.sales > 1000000)
  .map(album => `${album.artist} is a great performer`);
  console.log(popular);
```

