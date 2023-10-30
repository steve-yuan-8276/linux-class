---
title: 'React 学习笔记（二）： 初学者常见知识'
date: 2018-02-27 20:55:29
categories: react 
tags: [react, node, npm] 
---

### TIP 1: 正确使用大小写

#### 组件名称必须使用大写字母开头

比如下面的例子会报错，正确写法是Greeting **首字母大写**。

```
class greeting extends React.Component { 
  // ...
}
```

再比如：

正确|错误
---|---
React.Component|React.component
componentDidMount|ComponentDidMount
ReactDOM|ReactDom
props.userName|props.username或props.UserName

<!--more-->

### TIP 2：React.PropTypes 需要单独安装

PropTypes对象已从React中删除，直接调用会报错，需要用到的时候必须单独安装：

```
1. 将新的prop-types包添加到您的项目中：npm install prop-types

2. 导入包：`import PropTypes from 'prop-types'`
```

### TIP 3:  单引号(single quote)和反引号(backticks)用法不同

反引号或者叫倒引号，通常是英文状态下`tab`键上面的那个按键，里面可以是字符串模版，可以包含表达式。

单引号，通常可以跟双引号通用，引用的是普通字符串，不能包含表达式。

```
// 当前时间
const time = new Date().toLocaleTimeString();

// 当(单引号或双引号)时，
// 您需要使用字符串连接:
'Time is ' + time

// 使用倒引号时,可使用${}在字符串中插入时间
`Time is ${time}`
```
再比如，使用反引号可以跨行，单引号不可以。

```
const template = `I
CAN
SPAN
Multiple Lines`;
```

### TIP 4：不要将数字作为字符串传递进prop

字符串可以直接 单引号传进prop；如果是数字，则要用花括号

```
<Greeting name=‘World’ />      //字符串，OK

<Greeting name=‘World’ />     //数字，会报错

<Greeting counter={7} />     //数字，用花括号OK
```
### TIP 5: 自闭合标签

跟HTML当中类似，如果如果组件没有子内容，可以使用自闭合标签；反之，如有子内容，则要有开始和结束标签。

```
// 组建当中是空的，没有子内容，则下面两种方法都是有效的
<Greeting></Greeting>
<Greeting />

//组件有子内容，要有开始和结束标签
<Greeting>Hello!</Greeting>
```

### TIP 6:  componentDidMount() 生命周期事件

生命周期事件是组件中名称特殊的方法。这些方法会自动绑定到组件实例，React 将在组件生命周期的特定时间点调用这些方法。

以下是常见的生命周期事件：

方法|使用场景
---|---
componentWillMount()|在组件插入 DOM 之前立即被调用
componentDidMount()|在组件插入 DOM 之后立即被调用
componentWillUnmount()|在组件从 DOM 中删除之前立即被调用
componentWillReceiveProps()|每当组件即将接收全新的属性时被调用

其中，最常用的componentDidMount()，是组件添加到 DOM 之后立即运行的生命周期钩子，如果你想获取远程数据或发出 Ajax 请求，则应该使用该方法。

举个例子：

```
import React, { Component } from 'react';
import fetchUser from '../utils/UserAPI';

class User extends Component {
  constructor(props) {
    super(props)

    this.state = {
      name: '',
      age: ''
    }
  }

  componentDidMount() {
    fetchUser().then((user) => this.setState({
      name: user.name,
      age: user.age
    }))
  }

  render() {
    return (
      <div>
        <p>Name: {this.state.name}</p>
        <p>Age: {this.state.age}</p>
      </div>
    )
  }
}

export default User;
```
##### 1. render() 方法被调用，然后它会更新具有<div> 的页面（一段是名称，一段是年龄）。值得注意的是，this.state.name 和 this.state.age 是空字符串（一开始），所以名称和年龄实际上并不显示出来。

##### 2. 组件被装载后，发生 componentDidMount() 生命周期事件。

* 运行 UserAPI 的 fetchUser 请求，它会向用户数据库发出请求；

* 返回数据后，setState() 被调用，并更新 name 和 age 属性

##### 3. 因为状态已变化，render() 再次被调用。这样就会重新渲染页面，但是现在 this.state.name 和 this.state.age 具有值了。


