---
title: freeCodeCamp错题集：JS部分
date: 2017-12-26 15:37:54
categories: freeCodeCamp 
tags: [freeCodeCamp, JavaScript] 
---

### 206题：21点算法

#### 问题描述：

二十一点规则 ：

1. 游戏由玩家和庄家（即赌场的发牌员）对玩，看谁的牌面点数更靠近21点。但如果超过了21点，则称为“爆掉”，算输。其中花牌（J，Q，K）都算10点，A可以算1点，也可以算11点，看哪种情况更有利。玩家之间不做比较。

 
2. 游戏开始时，所有玩家和庄家各拿两张牌，一般来说，是玩家两张牌牌面朝上，庄家一张牌面朝上，一张牌面朝下。 

3. 两张牌的点数，肯定介于2到21点之间。21点只可能是一张10（包括J，Q，K，下同）和一张A，这叫“天成（BlackJack，以下简称BJ）”，除非庄家也拿到了BJ，不然赢一倍半的赌注。

![](https://farm5.staticflickr.com/4622/40100610072_e4c4ebba32_o.jpg)

解题方法：

```
function cc(card) {
  switch(card){
    case 2:
    case 3:
    case 4:
    case 5:
    case 6:
      count++;
      break;
    case 10:
    case "J":
    case "Q":
    case "K":
    case "A":
      count--;
      break;
  }
      
      if(count>0){
      return count+" Bet";
  }else if(count<=0){
      return count+" Hold";
  }
}
```
<!--more-->

### 225题：二维数组
如果你有一个二维数组，可以使用相同的逻辑，先遍历外面的数组，再遍历里面的子数组。下面是一个例子：

```
var arr = [
[1,2], [3,4], [5,6]
];
for (var i=0; i < arr.length; i++) {
for (var j=0; j < arr[i].length; j++) {
​ console.log(arr[i][j]);
}
}
```

一次输出 arr 中的每个子元素。提示，对于内部循环，我们可以通过 arr[i] 的 .length 来获得子数组的长度，因为 arr[i] 的本身就是一个数组。

#### 问题描述：
修改函数 multiplyAll，获得 arr 内部数组的每个数字相乘的结果 product。

#### 问题解答：
```
function multiplyAll(arr) {
  var product = 1;
  // Only change code below this line
  for (var i = 0; i < arr.length; i++) {
    for (var j = 0; j < arr[i].length; j++)
      product *= arr[i][j];
  }
  // Only change code above this line
  return product;
}

// Modify values below to test your code
multiplyAll([[1,2],[3,4],[5,6,7]]);

```

### 对象数组
函数 lookUp 有两个预定义参数：firstName值和prop属性 。

函数将会检查通讯录中是否存在一个与传入的 firstName 相同的联系人。如果存在，那么还需要检查对应的联系人中是否存在 prop属性。

如果它们都存在，函数返回prop属性对应的值。

如果firstName 值不存在，返回 "No such contact"。

如果prop 属性不存在，返回 "No such property"。

```
//Setup
var contacts = [
    {
        "firstName": "Akira",
        "lastName": "Laine",
        "number": "0543236543",
        "likes": ["Pizza", "Coding", "Brownie Points"]
    },
    {
        "firstName": "Harry",
        "lastName": "Potter",
        "number": "0994372684",
        "likes": ["Hogwarts", "Magic", "Hagrid"]
    },
    {
        "firstName": "Sherlock",
        "lastName": "Holmes",
        "number": "0487345643",
        "likes": ["Intriguing Cases", "Violin"]
    },
    {
        "firstName": "Kristian",
        "lastName": "Vos",
        "number": "unknown",
        "likes": ["Javascript", "Gaming", "Foxes"]
    }
];

function lookUp(firstName, prop){
// Only change code below this line
  for (i = 0; i < contacts.length; i++) {
    if (contacts[i].firstName == firstName) {
      if (contacts[i].hasOwnProperty(prop)) {
        return contacts[i][prop];
      }
        return "No such property";
    }
  }
    return "No such contact";
// Only change code above this line
}

// Change these values to test your function
lookUp("Akira", "likes");

```
### 生成随机数
要生成两个指定的数之间的随机数，首先需要定义一个最小值和一个最大值。

下面是我们将要使用的方法，仔细看看并尝试理解这行代码到底在干嘛：

```
Math.floor(Math.random() * (max - min + 1)) + min


```
#### 问题描述：

创建一个叫randomRange的函数，参数为myMin和myMax，返回一个在myMin(包括myMin)和myMax(包括myMax)之间的随机数。

#### 问题解答：

```
function ourFunction(ourMin, ourMax) {

  return Math.floor(Math.random() * (ourMax - ourMin + 1)) + ourMin;
}

ourFunction(1, 9);

// Only change code below this line.

function randomRange(myMin, myMax) {

  return Math.floor(Math.random() * (myMax - myMin + 1)) + myMin; // Change this line

}

// Change these values to test your function
var myRandom = randomRange(5, 15);

```

### 构造函数的属性
对象拥有自己的特征，称为 属性，对象还有自己的函数，称为 方法 。

在前面的课程（构造函数）中，我们使用了 this 指向当前（将要被创建的）对象中的 公有属性 。

我们也可以创建 私有属性 和 私有方法 ，它们两个在对象外部是不可访问的。

为了完成这个任务，我们在 构造函数 中，使用我们熟悉的 var 关键字去创建变量，来替代我们使用 this 创建 属性 。

比如，我们想记录我们的car行驶的 speed ，但是我们希望外面的代码对 speed 的修改只能是加速或减速（而不是变成字符串、直接赋值成某个速度等其他操作），那么如何达到这类操作的目的呢？

```
var Car = function() {
  // this is a private variable
  var speed = 10;

  // these are public methods
  this.accelerate = function(change) {
    speed += change;
  };

  this.decelerate = function() {
    speed -= 5;
  };

  this.getSpeed = function() {
    return speed;
  };
};

var Bike = function() {

  // 只能在这一行下面写代码
  var gear = 10;

  // these are public methods
  this.setGear = function(change) {
    gear = change;
  };

  this.getGear = function() {
    return gear;
  };
};

var myCar = new Car();

var myBike = new Bike();

```


