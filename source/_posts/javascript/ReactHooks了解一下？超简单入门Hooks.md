---
title: React Hooks了解一下？超简单入门Hooks
date: 2021-06-10 16:50:27
categories: 前端
---
## 前言

> 先简单说一下Hooks是什么，在写React组件时候，有两种写法，一种是类写法，一种就是函数写法，Hooks主要服务的对象还是函数组件，因为函数组件相对类组件有些功能确实不好实现，比如生命周期钩子，Hooks就是让函数组件具有类组件能力的一个东西，Hooks的出现会给我们带来另外一种组件实现思路，一直看到有些小伙伴们问Hooks会不会代替Redux，emmm...我觉得这应该不是一个概念吧，这些小伙伴不必担心。
> 
> Tips：按照惯例声明，笔者也是入门级选手，所写文章深度不会达到大佬的文章水平，只希望能对像我一样的入门级小伙伴有所帮助。

## 文字说明会很长，只有例子最明了

先来看一个可以管理状态的组件在React中怎样写

```
import React from 'react'

class Counter extends React.Component {
    state = {
        count: 0
    }

    componentDidMount() {
        this.interval = setInterval(() => {
            this.setState({
                count: this.state.count + 1
            })
        }, 1000);
    }

    componentWillUnmount() {
        if(this.interval) {
            clearInterval(this.interval)
        }
    }

    render() {
        return (
            <div>count is : {this.state.count}</div>
        )
    }
}

export default Counter;
复制代码
```

以上代码写了一个简单的计数器组件，管理了一个count状态（就是一个名字叫count的数据），在组件加载完成后，执行定时器，将count状态每秒+1，在组件要被卸载之前清除定时器(不清除会一直占用内存，可能导致内存泄漏)。这里添加定时器和卸载定时器都是通过生命周期钩子来实现的，在Hooks引入之前，函数组件无法使用生命周期函数的，所以无法完成上述的状态管理功能。

**引入Hooks，让函数组件也可以完成状态管理**

先简单使用两个Hooks，下文还会详细介绍

```
import React, {useState, useEffect} from 'react'

function CounterFunc() {
    const [count, setCount] = useState(0)

    useEffect(() => {
        const interval = setInterval(() => {
            setCount(c => c + 1)
        },1000)

        return () => clearInterval(interval)
    },[])

    return (
        <div>count is : {count}</div>
    )
}

export default CounterFunc;
复制代码
```

这里直接上了一段代码，在最开头引入了两个函数:`useState`和`useEffect`。

在函数组件CounterFunc中，通过`useState(0)`来设置count的值，这里`useState(0)`会返回一个数组，数组第一个元素就是被赋值为0的变量，第二个元素是可以对第一个元素重新设置值得函数。我们这里通过数组解构的方式，将这两个元素分别赋值给`count`和`setCount`，(如果不了解ES6的解构赋值，请先查阅阮一峰老师ES6书籍的相关部分)。

我们还用了`useEffect()`函数，在函数里定义了一个定时器，然后返回了一个函数，在返回的函数中清除了定时器，这个被返回的函数就相当于类组件中的`componentWillUnmount()`生命周子函数了。而这个`useEffect()`函数本身又相当于`componentDidMount()`生命周期函数。

至此我们也能很轻松理解`useState`其实就相当于类组件中设置state的部分，它返回的数组第二个元素`setCount()`是一个高阶函数，我们需要传入一个`c => c + 1`作为参数，这个高阶函数会将`count`传进`c => c + 1`并将计算后的值重新赋值给count。

![image.png](https://upload-images.jianshu.io/upload_images/10024246-9c5d0a962eecdbcf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## State Hooks

### useState()

> 对于状态管理的Hooks，上面已经介绍了`useState()`，这里会再加一个`useReducer()`,`useReducer`才是基础的状态管理Hook

**useState(0)**

对useState传递的0只是对状态设置了一个默认值，状态改变后他就没用了。

它返回的两个元素我想大家应该也都清楚是什么东西了，但是这里要说的是第二个元素，它有两种用法

*   `setCount( 100 )`传递一个确定的值
*   `setCount( c => c + 1)`传递一个函数，我们上面使用的方式

这里说明一下我们上面为什么不用`setCount( count + 1 )`的方式，有兴趣的小伙伴可以试试，这样我们页面的结果始终都是1，而不是预期的1,2,3...... 为什么会发生这种事？这是由于闭包造成的，在state更新后，会重新执行我们创建的函数组件，这时候`const [count, setCount] = useState(0)`这段代码会重新执行一遍，count每次都会被重置为0，然后更新为1。

### 闭包插曲：

> 上面说到了闭包，本来不想单独说闭包，但是想了想，Hooks应该算是走函数式编程的道路吧，在学Hooks时候，我们会遇到很多奇怪的现象，导致小伙伴感觉很难，其实很大程度上跟没掌握好闭包有很大关系。这里就简单说一下。
> 
> Tips: 请注意，如果你看别的文章从未搞懂过闭包，别担心，这里我还是有信心让90%看我文章的人搞清楚什么是闭包。有基础的直接跳过！！！

**要说闭包先说作用域**

我们这里撇开let,const定义变量的方式，回归初心var 在JavaScript中，ES6出现之前是没有块级作用域的，但是它有个东西叫做函数作用域

```
var glob = '我是最外层定义的变量'

function func1() {
    console.log(glob) // 这里可以引用上层的变量
    var f1 = '我是func1中的变量'

    function func1_1() {
        var f1_1 = '我是函数func1_1中的变量'
        console.log(glob) 
        console.log(f1)  // 这里可以拿到上一层函数中定义的变量和最外层的变量
    }
    function func1_1() {
        console.log(glob) // 这里可以拿到最外层的变量
        console.log(f1_1) // 但是拿不到同一级别函数定义的变量
    }

}
复制代码
```

上面这代码和注释相信能让大家看懂一点JavaScript中的作用域。一个函数，是不能拿到同级别函数里面定义的变量，但是能拿到父级，爷爷级，爷爷爷级函数定义的变量。我们可以利用这里特性对变量进行封装。

```
function func1(param) {
    var f1 = '只让我的子孙拿到的变量‘
    var f2 = param; // 这里把param赋值给f2，它也只能被自己子孙拿到

    function func1_1() {
        console.log(f1, f2) //都能拿到
    }

    return func1_1
}

console.log(f1, f2) //两个都拿不到，报错

var res1 = func1('hello') // 你猜他的结果是什么。  

// 它的结果是一个函数，就是我们返回的func1_1这个函数，猜错的自己罚自己，给我点个赞，哈哈哈。

res1() // res1存放的就是返回的func1_1,所以再把它执行一遍 
//打印出 `只让我的子孙拿到的变量hello`

复制代码
```

其实在我们执行func1('hello')之后，func1的生命周期就结束了，本来，它里面定义的f1,f2都应该在它生命周期结束后就再也访问不到了，但是幸运的是，func1_1替他保存下了这两个变量，使得`func1还没有死透`

这样封装变量就是闭包。

> 闭包插曲结束

### 继续上面的来说

我们可以通过`setCount(c => c + 1)`传入回调函数的方式避免闭包带来的`奇怪`现象。

### useReducer

```
function countReducer(state, action) {
    switch(action.type) {
        case 'add': 
            return state + 1;
        case 'minus': 
            return state - 1;
        default:
            return state
    }
}

......
// const [count, setCount] = useState(0)
const [count, dispatchCount] = useReducer(countReducer,0);
......
// setCount(c => c + 1)
dispatchCount({ type: 'add' })
......
复制代码
```

首先我们先在CounterFunc外面定义一个reducer,然后更改count的定义，以及count的更新，这里useReducer的使用跟 redux中操作很像，后面会持续更新Redux的使用，请有需要的小伙伴关注一下，顺手给个赞。

要注意的是，`useReducer(countReducer,0)`的两个参数，第一个是定义的countReducer第二个是默认值。我们接收返回值的第二个元素固定写为dispach，或者这里写的dispachCount的格式。

## 小结

> 这里主要分享了5个知识点，一个是函数组件与类组件的区别，一个是useState, 再一个useEffect（后面会详细说）,然后介绍了闭包，最后说明了useReducer的使用，相信认真看到这里的小伙伴对于React的Hooks已经有了个初步了解，并且对闭包不熟悉的小伙伴也已经对闭包有了深入认识吧。

>作者：king王一帅
链接：https://juejin.cn/post/6844903830841196552
