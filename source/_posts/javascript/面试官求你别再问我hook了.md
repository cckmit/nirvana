---
title: 面试官求你别再问我hook了
date: 2022-03-22 16:28:03
categories: 前端
tags: [react,面试]
---
# 一 前言

先问大家几个问题，这几个问题都是我在面试中真实被问到的，属实给我整不会了....

*   写`hooks`跟写类组件比，`hooks`有啥优势？
*   我们如何封装一个`hook`？
*   `hooks`原理是什么？

面试虽然凉了，但是学习还得继续😭

# 二 深入了解hooks

## useState

*   使用

`useState`的使用很简单🙉，一句话带过，返回一个数组，数组的值为当前`state`和更新`state`的函数；`useState`的参数是**变量、对象或者是函数**，变量或者对象会作为`state`的初始值，如果是函数，函数的返回值会作为初始值。

*   批量更新

看下面这段代码

```
function Count(){
    let [count,setCount] = useState(0)
    const handleAdd = function(){
        setCount(count+1)
        setCount(count+1)
    }
    return(
        <div>
            <p>{count}</p>    
            /*每次点击加1*/
            <button onClick={handleAdd}>加一</button>
        </div>
    )
}
```

在同一个事件中并不会因为调用了两次`setCount`而让`count`增加两次，试想如果在同一个事件中每次调用`setCount`都生效，那么每调用一次`setCount`组件就会重新渲染一次，这无疑使非常影响性能的；实际上如果修改的`state`是同一个，最后一个`setCount`函数中的新`state`会覆盖前面的

## useEffect && useLayoutEffect

*   使用

这两个`hook`用法一致，第一个参数是回调函数，第二个参数是数组，数组的内容是依赖项`deps`,依赖项改变后执行回调函数；注意组件每次渲染会默认执行一次,如果不传第二个参数只要该组件有`state`改变就会触发回调函数,如果传一个空数组，只会在初始化执行一次。另外，如果用`return`返回了一个函数，组件每次重新渲染的时候都会先执行该函数再调用回调函数。

*   区别

表面上看，这两个`hook`的区别是执行时机不同，`useEffect`的回调函数会在页面渲染后执行；`useLayoutEffect`会在页面渲染前执行。实际上是`React`对这两个`hook`的处理不同，`useEffect`是异步调用，而`useLayoutEffect`是同步调用。
那什么时候用`useEffect`，什么时候用`useLayoutEffect`呢？
我的理解是视情况而定 如果回调函数会修改`state`导致组件重新渲染,可以`useLayoutEffect`，因为这时候用`useEffect`可能会造成页面闪烁； 如果回调函数中去请求数据或者js执行时间过长，建议使用`useEffect`；因为这时候用`useLayoutEffect`堵塞浏览器渲染。

## useMemo && useCallback

这两个`hook`可用于性能优化，减少组件的重复渲染；现在就来看看这两个神奇的`hook`怎么用。

*   uesMemo

```
function MemoDemo() {
    let [count, setCount] = useState(0);
    let [render,setRender] = useState(false)
    const handleAdd = () => {
        setCount(count + 1);
    };
    const Childone = () => {
        console.log("子组件一被重新渲染");
        return <p>子组件一</p>;
    };
    const Childtwo = (props) => {
        return (
            <div>
                <p>子组件二</p>
                <p>count的值为：{props.count}</p>
            </div>
        );
    };
    const handleRender = ()=>{
        setRender(true)
    }
    return (
        <div style={{display:"flex",justifyContent:'center',alignItems:'center',height:'100vh',flexDirection:'column'}}>
            {
                useMemo(() => {
                    return <Childone />
                }, [render])
            }
            <Childtwo count={count} />
            <button onClick={handleAdd}>增加</button>
            <br/>
            <button onClick={handleRender} >子组件一渲染</button>
        </div>
    );
}
```

`Childone`组件只有`render`改变才会重新渲染

这里顺带讲下，`React.memo`,用`React.memo`包裹的组件每次渲染时会和`props`会和旧的`props`进行浅比较，如果没有变化则组件不渲染；示例如下

```
const Childone = React.memo((props) => {
    console.log("子组件一被重新渲染",props);
    return <p>子组件一{props.num}</p>;
})
function MemoDemo() {
    let [count, setCount] = useState(0);
    let [render,setRender] = useState(false)
    let [num,setNum] = useState(2)
    const handleAdd = () => {
        setCount(count + 1);
    };

    const Childtwo = (props) => {
        return (
            <div>
                <p>子组件二</p>
                <p>count的值为：{props.count}</p>
            </div>
        );
    };
    const handleRender = ()=>{
        setRender(true)
    }
    return (
        <div style={{display:"flex",justifyContent:'center',alignItems:'center',height:'100vh',flexDirection:'column'}}>
            {/* {
                useMemo(() => {
                    return <Childone />
                }, [render])
            } */}
            <Childone num={num}/>
            <Childtwo count={count} />
            <button onClick={handleAdd}>增加</button>
            <br/>
            <button onClick={handleRender} >子组件一渲染</button>
        </div>
    );
}
```

这个例子是把上个例子中的`Childone`拆出来套上`React.memo`的结果，点击增加后组件不会该组件不会重复渲染，因为`num`没有变化

*   useCallback

还是上面那个例子，我们把`handleRender`用`useCallback`包裹，也就是说这里`num`不变化每次都会传同一个函数，若是这里不用`useCallback`包裹，每次都会生成新的`handleRender`，导致`React.memo`函数中的`props`浅比较后发现生成了新的函数，触发渲染

```
const Childone = React.memo((props) => {
    console.log("子组件一被重新渲染",props);
    return <p>子组件一{props.num}</p>;
})
export default function MemoDemo() {
    let [count, setCount] = useState(0);
    let [render,setRender] = useState(false)
    let [num,setNum] = useState(2)
    const handleAdd = () => {
        setCount(count + 1);
    };

    const Childtwo = (props) => {
        return (
            <div>
                <p>子组件二</p>
                <p>count的值为：{props.count}</p>
            </div>
        );
    };
    const handleRender = useCallback(()=>{
        setRender(true)
    },[num])
    return (
        <div style={{display:"flex",justifyContent:'center',alignItems:'center',height:'100vh',flexDirection:'column'}}>
            {/* {
                useMemo(() => {
                    return <Childone />
                }, [render])
            } */}
            <Childone num={num} onClick={handleRender}/>
            <Childtwo count={count} />
            <button onClick={handleAdd}>增加</button>
            <br/>
            <button onClick={handleRender} >子组件一渲染</button>
        </div>
    );
}
```

## useRef

这个`hook`通常用来获取组件实例，还可以用来缓存数据❗ 获取实例就不过多解释了，需要注意的是只有类组件才有实例；
重点看下`useRef`如何缓存数据的：

```
function UseRef() {
    let [data, setData] = useState(0)
    let initData = {
        name: 'lisa',
        age: '20'
    }
    let refData = useRef(initData)   //refData声明后组件再次渲染不会再重新赋初始值
    console.log(refData.current);
    refData.current = {       //修改refData后页面不会重新渲染
        name: 'liyang ',
        age: '18'
    }
    const reRender = () => {
        setData(data + 1)
    }
    return (
        <div>
            <button onClick={reRender}>点击重新渲染组件</button>
        </div>
    )
}
```

组件重新渲染后，变量会被重新赋值，可以用`useRef`缓存数据，这个数据改变后是不会触发组件重新渲染的，如果用`useState`保存数据，数据改变后会导致组件重新渲染，所以我们想悄悄保存数据，`useRef`是不二选择👊

# 三 自定义hook

自定义`hook`，也就是`hook`的封装，至于为什么要封装`hook`呢？`react`官网给出了答案

> 使用 Hook 其中一个[目的](https://link.juejin.cn?target=https%3A%2F%2Freact.docschina.org%2Fdocs%2Fhooks-intro.html%23complex-components-become-hard-to-understand "https://react.docschina.org/docs/hooks-intro.html#complex-components-become-hard-to-understand")就是要解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。 通过自定义 Hook，可以将组件逻辑提取到可重用的函数中。

先来看下这个例子：

```
export default function CustomHook() {
    let refone = useRef(null)
    let X, Y, isMove = false,left,top
    //基于鼠标事件实现拖拽
    useEffect(() => {
        const dom = refone.current
        dom.onmousedown = function (e) {
            isMove = true
            X = e.clientX - dom.offsetLeft;
            Y = e.clientY - dom.offsetTop;
        }
        dom.onmousemove = function (e) {
            if (isMove) {
                left = e.clientX - X
                top = e.clientY - Y
                dom.style.top = top + "px"
                dom.style.left = left + "px"
            }

        }
        dom.onmouseup = function (e) {
            isMove = false
        }
    }, [])
    return (
        <div style={{ display: "flex", justifyContent: 'center', alignItems: 'center', height: '100vh' }}>
            <div ref={refone} style={{ width: '70px', height: '70px', backgroundColor: '#2C6CF9',position:'absolute' }}></div>
        </div>
    )
}
```

我们利用鼠标事件简单的实现了一个拖拽方格的效果，那如果在其他页面也需要这个效果呢？😏于是，我们可以考虑把这段相同的逻辑封装起来，就像我们提取公共组件一般。来看下面这个例子：

```
import {useEffect, useRef } from "react";
function useDrop() {
    let refone = useRef(null)
    let X, Y, isMove = false,left,top
    //基于鼠标事件实现拖拽
    useEffect(() => {
        const dom = refone.current
        dom.onmousedown = function (e) {
            isMove = true
            X = e.clientX - dom.offsetLeft;
            Y = e.clientY - dom.offsetTop;
        }
        dom.onmousemove = function (e) {
            if (isMove) {
                left = e.clientX - X
                top = e.clientY - Y
                dom.style.top = top + "px"
                dom.style.left = left + "px"
            }

        }
        dom.onmouseup = function (e) {
            isMove = false
        }
    }, [])
    return refone
}
export default function CustomHook() {
    let refone = useDrop()
    let reftwo = useDrop()
    return (
        <div style={{ display: "flex", justifyContent: 'center', alignItems: 'center', height: '100vh' }}>
            <div ref={refone} style={{ width: '70px', height: '70px', backgroundColor: '#2C6CF9',position:'absolute' }}></div>
            <div ref={reftwo} style={{ width: '70px', height: '70px', backgroundColor: 'red',position:'absolute' }}></div>
        </div>
    )
    }
```

这里为来减少代码量只展示了在同一个页面使用封装过的`hook`，事实上封装这段`hook`我们只改了几行代码，却实现了逻辑的重用是不是很神奇！😆需要注意的是`hook`的封装函数必须要以`use`开头，因为使用`hook`本身是有规则的，比如不能在条件语句中使用`hook`,不能在函数外使用`hook`;如果不适用`use`开头封装`hook`，则`react`无法自动检查该函数内使用的`hook`是否符合规则。

>作者：食困症患者
链接：https://juejin.cn/post/7019493055396839431
