---
title: 'freeCodeCamp笔记： 算法习题集'
date: 2017-12-27 17:49:49
categories: freeCodeCamp
tags: [freeCodeCamp, JavaScript] 
---

### 写在前面
终于到了算法习题部分了，都说这部分比较好玩，所以专门整理出来，以后随时可以回来看。

#### 翻转字符串
先把字符串转化成数组，再借助数组的reverse方法翻转数组顺序，最后把数组转化成字符串。

```
function reverseString(str) {
  // 请把你的代码写在这里
  var arr = str.split('');
  arr.reverse();
  var joinStr = arr.join('');
  return joinStr;
}

reverseString("hello");

```
<!--more-->

#### 计算一个整数的阶乘

如果用字母n来代表一个整数，阶乘代表着所有小于或等于n的整数的乘积。

阶乘通常简写成 n!
例如: 5! = 1 * 2 * 3 * 4 * 5 = 120

```
function factorialize(num) {
  // 请把你的代码写在这里
  if(num < 0) {
    return "输入错误";
  } else if(num === 0 || num === 1) {
    return 1;
  } else {
      for (var i = num - 1; i >= 1; i--) { 
        num *= i; 
      }
  }
  return num;
}
factorialize(5);

```

#### 检查回文字符串

如果一个字符串忽略标点符号、大小写和空格，正着读和反着读一模一样，那么这个字符串就是palindrome(回文)。

如果给定的字符串是回文，返回true，反之，返回false。

```
function palindrome(str) {
  // 请把你的代码写在这里 
  str = str.replace(/[\ |\~|`|\!|\@|\#|\$|\%|\^|\&|\*|\(|\)|\-|\_|\+|\=|\||\|\[|\]|\{|\}|\;|\:|\"|\'|\,|\<|\.|\>|\/|\?]/g,"");     //str.replace()+正则表达式匹配,去掉字符串中的标点符号和空白格。
  var newStr = str.toLowerCase();
  var arr = newStr.split('').reverse();   //先用str.split 拆成数组，在用arr.reverse 翻转排序
  var reverseStr = arr.join('');
  if(newStr === reverseStr) {
    return true;
  } else {
    return false;
  }
}

palindrome("0_0 (: /-\ :) 0-0abcd");
```
注：本题难点在于 str.replace + reqular Expressions  匹配正字进行替换。

#### 找出最长单词

在句子中找出最长的单词，并返回它的长度。

函数的返回值应该是一个数字。

```
function findLongestWord(str) {
  // 请把你的代码写在这里
  var length = 0;
  var arr = str.split(' ');
  for(var i = 0; i <arr.length; i++) {
    if(arr[i].length > length) {
      length = arr[i].length;
      } 
  }
  return length;
}

findLongestWord("The quick brown fox jumped over the lazy dog");

```
注：本题核心是考察for循环和if判断

#### 句中单词首字母大写
确保字符串的每个单词首字母都大写，其余部分小写。

```
function titleCase(str) {
  // 请把你的代码写在这里
  var arr = str.toLowerCase().split(' ');
  for(var i = 0; i < arr.length; i++) {
    arr[i] = arr[i][0].toUpperCase() + arr[i].slice(1);
    }

  return arr.join(' ');
}

titleCase("I'm a little tea pot");
```


#### 找出多个数组中的最大数

右边大数组中包含了4个小数组，分别找到每个小数组中的最大值，然后把它们串联起来，形成一个新数组。

```
function largestOfFour(arr) {
  // 请把你的代码写在这里
  var largestOfFour = [];

  for(m = 0; m < arr.length; m++) {
    var maxNum = 0;
    for(n = 0; n < arr[m].length; n++) { 
      if(maxNum < arr[m][n]){
        maxNum = arr[m][n];
      }
    }
    largestOfFour.push(maxNum);
  }
  return largestOfFour;
}

largestOfFour([[13, 27, 18, 26], [4, 5, 1, 3], [32, 35, 37, 39], [1000, 1001, 857, 1]]);
```

#### 检查字符串结尾

判断一个字符串(str)是否以指定的字符串(target)结尾。

如果是，返回true;如果不是，返回false。

```
function confirmEnding(str, target) {
  // 请把你的代码写在这里
  if(str.substr((str.length - target.length), target.length) === target) {
    return true;
  } else {
    return false;
  }

}

confirmEnding("Bastian", "n");
```

 注：重点是考察 string.substr() 的基本用法，这个函数有两个参数，第一个表示起始位置，第二个表示提取字符串的长度。这里提取字符串的长度是由target决定的，所以要用 target.length 
 
####  重要的事情说N遍

重复一个指定的字符串 num次，如果num是一个负数则返回一个空字符串。

```
function repeat(str, num) {
  // 请把你的代码写在这里
  if(num > 0) {
    return str.repeat(num);
  } else {
    return '';
  } 
}

repeat("abc", 3);
```
此外，还有一种方法：

```
function repeat(str, num) {
  // 请把你的代码写在这里
  var result = '';
  while(num > 0) {
    result += str;
    num--;
  }
repeat("abc", 3);
```

#### 截断字符串

如果字符串的长度比指定的参数num长，则把多余的部分用...来表示;

```
function truncate(str, num) {
  // 请把你的代码写在这里
  if(str.length > num) {
    if(num > 3) {
      return str.substr(0,num-3) + '...';
    } else {
      return str.substr(0,num) + '...';
    }
  } else {
    return str;
  }
}

truncate("A-tisket a-tasket A green and yellow basket", 11);

```
注：这里有一个trick，就是插入到字符串尾部的三个点号也会计入字符串的长度。

#### 猴子吃香蕉, 分割数组

把一个数组arr按照指定的数组大小size分割成若干个数组块。

例如:chunk([1,2,3,4],2)=[[1,2],[3,4]];

chunk([1,2,3,4,5],2)=[[1,2],[3,4],[5]];

```
function chunk(arr, size) {
  // 请把你的代码写在这里
  var newArr = [];
  for(i = 0; i < arr.length; i+= size) {
    newArr.push(arr.slice(i,i+size));
  }
  return newArr;
}

chunk(["a", "b", "c", "d"], 2);

```
注：arr.slice(start，end) 有两个参数，如果 end 大于数组长度，slice 也会一直提取到原数组末尾。

#### 截断数组

返回一个数组被截断n个元素后还剩余的元素，截断从索引0开始。

```
function slasher(arr, howMany) {
  // 请把你的代码写在这里
  var newArr = [];
  if(howMany == 1) {
    newArr = arr.slice(1);
    //return newArr;
  } else if(arr.length > howMany) {
    newArr = arr.slice(-(arr.length % howMany));
    //return newArr;
  } else {
    //return newArr;
  }
  return newArr;
}

slasher(["burgers", "fries", "shake"], 1);

```

#### 比较字符串

如果数组第一个字符串元素包含了第二个字符串元素的所有字符，函数返回true。

举例，["hello", "Hello"]应该返回true，因为在忽略大小写的情况下，第二个字符串的所有字符都可以在第一个字符串找到。

```
function mutation(arr) {
    var source = arr[0].toLowerCase();
    var target = arr[1].toLowerCase();
    for (var i = 0; i < target.length; i++) {
        if (source.indexOf(target[i]) === -1) {
            return false;
        }
    }
    return true;
}
```
[to be continue]

#### 过滤数组假值

删除数组中的所有假值。

在JavaScript中，假值有false、null、0、""、undefined 和 NaN。

**基础解法：for loop**

```
function bouncer(arr) {
  // 请把你的代码写在这里
  var result = [];
  for(var i = 0; i < arr.length; i++) {
    if(arr[i]) {
      result.push(arr[i]);
    }
  }
  return result;
}

bouncer([7, "ate", "", false, 9]);
```
**进阶解法：**

```
function bouncer(arr) {
  // 请把你的代码写在这里
  return arr.filter(Boolean);
}

bouncer([7, "ate", "", false, 9]);

```

#### 摧毁数组

实现一个摧毁(destroyer)函数，第一个参数是待摧毁的数组，其余的参数是待摧毁的值。

```
function destroyer(arr) {
  // 请把你的代码写在这里
  var newArr = [].slice.call(arguments); 
  var sourceArr = newArr[0];
  var boomArr = newArr.slice(1);
  
  for(var i = sourceArr.length-1; i >= 0; i--) {  //这样写循环，相当于从右向左遍历
    if(boomArr.indexOf(sourceArr[i]) > -1) {
      sourceArr.splice(i,1);
    }
  }
  return sourceArr;
}

destroyer([1, 2, 3, 1, 2, 3], 2, 3);
```

注：这个题比较做的比较费劲，因为不知道对象转换数组的用法、arr.splice()的用法，整理如下：

##### 对象转换数组
arguments 是一个对应于传递给函数的参数的类数组对象。arguments对象类似于Array，但不是一个 Array。

除了长度之外，arguments 没有任何Array属性，也没有 pop 方法。

arguments 转化成Array，实现方法有两种：

第一种：

```
Array.prototype.slice.call(arguments)
```
第二种：

```
[].slice.call(arguments)
```

#### splice() 方法

通过删除现有元素和/或添加新元素来更改一个数组的内容。

语法如下：

```
array.splice(start, deleteCount, item1, item2, ...)
```

###### start​ （必选）指定修改的开始位置（从0计数）。
* 如果超出了数组的长度，则从数组末尾开始添加内容；
* 如果是负值，则表示从数组末位开始的第几位（从1计数）；
* 如果只有start参数，没有deleteCount, item1，则表示删除的元素。

###### deleteCount （deleteCount （可选） 表示移除的数组元素的个数。

* 如果是 0，则不移除元素；
* 如果 deleteCount 大于start 之后的元素的总数，则从 start 后面的元素都将被删除（含第 start 位）；
* 如果被省略，则相当于(arr.length - start)。

###### item1, item2, ... 可选
要添加进数组的元素,从start 位置开始。如果不指定，则 splice() 将只删除数组元素。

#### 数组排序并找出元素索引

先给数组排序，然后找到指定的值在数组的位置，最后返回位置对应的索引。

```
function where(arr, num) {
    arr.push(num);
    
    arr.sort(function(a, b) {
        return a - b;
    });
    return arr.indexOf(num);
}
```

优化解法：

```
function where(arr, num) {
    return arr.filter(e => e < num).length;
}
```

### 凯撒密码

下面我们来介绍风靡全球的凯撒密码Caesar cipher，又叫移位密码，也就是密码中的字母会按照指定的数量来做移位，一个常见的案例就是ROT13密码，字母会移位13个位置。由'A' ↔ 'N', 'B' ↔ 'O'，以此类推。

写一个ROT13函数，实现输入加密字符串，输出解密字符串。所有的字母都是大写，不要转化任何非字母形式的字符(例如：空格，标点符号)，遇到这些特殊字符，跳过它们。

```
function rot13(str) { // LBH QVQ VG!
  // 请把你的代码写在这里
  var result = "";
      for (var i = 0; i < str.length; i++) {
          var currentCode = str[i].charCodeAt();
          if (currentCode > 90 || currentCode < 65) {
              // 非大写字符
              result += String.fromCharCode(currentCode);
          } else if (currentCode < 78) {
              // 大写字符 A - M
              result += String.fromCharCode(currentCode + 13);
          } else {
              // 大写字符 N - Z
              result += String.fromCharCode(currentCode - 13);
          }
      }
      return result;
}

rot13("SERR PBQR PNZC");  
```

注：本题其实靠的是字符编码，英文字母大写从65-90；小写字母从97-122。

