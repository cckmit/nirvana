---
title: 【转载】React新文档：不要滥用Ref哦～
date: 2022-06-23 14:27:21
categories: 前端
tags: [react]
---
>原文：https://segmentfault.com/a/1190000041991074
`React`新文档有个很有意思的细节：`useRef`、`useEffect`这两个`API`的介绍，在文档中所在的章节叫`Escape Hatches`（逃生舱）。

显然，正常航行时是不需要逃生舱的，只有在遇到危险时会用到。

如果开发者过多依赖这两个`API`，可能是误用。

在[React新文档：不要滥用effect哦](https://segmentfault.com/a/1190000041942007)中我们谈到`useEffect`的正确使用场景。

今天，我们来聊聊`Ref`的使用场景。

## 为什么是逃生舱？

先思考一个问题：为什么`ref`、`effect`被归类到**逃生舱**中？

这是因为二者操作的都是**脱离React控制的因素**。

`effect`中处理的是**副作用**。比如：在`useEffect`中修改了`document.title`。

`document.title`不属于`React`中的状态，`React`无法感知他的变化，所以被归类到`effect`中。

同样，**使DOM聚焦**需要调用`element.focus()`，直接执行`DOM API`也是不受`React`控制的。

虽然他们是**脱离React控制的因素**，但为了保证应用的健壮，`React`也要尽可能防止他们失控。

## 失控的Ref

对于`Ref`，什么叫失控呢？

首先来看**不失控**的情况：

*   执行`ref.current`的`focus`、`blur`等方法
*   执行`ref.current.scrollIntoView`使`element`滚动到视野内
*   执行`ref.current.getBoundingClientRect`测量`DOM`尺寸

这些情况下，虽然我们操作了`DOM`，但涉及的都是**React控制范围外的因素**，所以不算失控。

但是下面的情况：

*   执行`ref.current.remove`移除`DOM`
*   执行`ref.current.appendChild`插入子节点

同样是操作`DOM`，但这些属于**React控制范围内的因素**，通过`ref`执行这些操作就属于失控的情况。

举个例子，下面是[React文档中的例子](https://link.segmentfault.com/?enc=0SJgUz%2FNMqSvmB8TCqYOGg%3D%3D.OuLh186r4bL8UJMcKEWDOJHdauwbGpoYOLjNBHcJ%2Bg6n6Tf3EGROQoEcAhDBBZWTwTej5N7Nb%2BW42rEXdxOaOg%3D%3D)：

**按钮1**点击后会插入/移除 P节点，**按钮2**点击后会调用`DOM API`移除P节点：

```
export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        Toggle with setState
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}
```

**按钮1**通过`React`控制的方式移除P节点。

**按钮2**直接操作`DOM`移除P节点。

如果这两种**移除P节点**的方式混用，那么先点击**按钮1**再点击**按钮2**就会报错：

[图片上传失败...(image-3c1a55-1655474541655)] 

这就是**使用Ref操作DOM造成的失控情况**导致的。

## 如何限制失控

现在问题来了，既然叫**失控**了，那就是`React`没法控制的（`React`总不能限制开发者不能使用`DOM API`吧？），那如何限制失控呢？

在`React`中，组件可以分为：

*   高阶组件
*   低阶组件

**低阶组件**指那些**基于DOM封装的组件**，比如下面的组件，直接基于`input`节点封装：

```
function MyInput(props) {
  return <input {...props} />;
}
```

在**低阶组件**中，是可以直接将`ref`指向`DOM`的，比如：

```
function MyInput(props) {
  const ref = useRef(null);
  return <input ref={ref} {...props} />;
}
```

**高阶组件**指那些**基于低阶组件封装的组件**，比如下面的`Form`组件，基于`Input`组件封装：

```
function Form() {
  return (
    <>
      <MyInput/>
    </>
  )
}
```

**高阶组件**无法直接将`ref`指向`DOM`，这一限制就将**ref失控**的范围控制在单个组件内，不会出现跨越组件的**ref失控**。

以[文档中的示例](https://link.segmentfault.com/?enc=bDZ1T1BRHpL3oXU8vF7%2FyA%3D%3D.swKEIVUsqqJNWmgrjygS%2B58dcuVimHfqHxmeQxqBNomvS4nKVekYy1vCDUg6XubqMLtZ%2BwCPh2eNDFdwTcG%2F1g%3D%3D)为例，如果我们想在`Form`组件中点击按钮，操作`input`聚焦：

```
function MyInput(props) {
  return <input {...props} />;
}

function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        input聚焦
      </button>
    </>
  );
}
```

点击后，会报错：

![image.png](https://upload-images.jianshu.io/upload_images/10024246-aa663bb0939ab68c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是因为在`Form`组件中向`MyInput`传递`ref`失败了，`inputRef.current`并没有指向`input`节点。

究其原因，就是上面说的**为了将ref失控的范围控制在单个组件内，React默认情况下不支持跨组件传递ref**。

## 人为取消限制

如果一定要取消这个限制，可以使用`forwardRef API`显式传递`ref`：

```
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});

function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

使用`forwardRef`（`forward`在这里是**传递**的意思）后，就能跨组件传递`ref`。

在例子中，我们将`inputRef`从`Form`跨组件传递到`MyInput`中，并与`input`产生关联。

在实践中，一些同学可能觉得`forwardRef`这一`API`有些多此一举。

但从**ref失控**的角度看，`forwardRef`的意图就很明显了：既然开发者手动调用`forwardRef`破除**防止ref失控的限制**，那他应该知道自己在做什么，也应该自己承担相应的风险。

同时，有了`forwardRef`的存在，发生**ref相关错误**后也更容易定位错误。

## useImperativeHandle

除了**限制跨组件传递ref**外，还有一种**防止ref失控的措施**，那就是`useImperativeHandle`，他的逻辑是这样的：

既然**ref失控**是由于**使用了不该被使用的DOM方法**（比如appendChild），那我可以限制**ref中只存在可以被使用的方法**。

用`useImperativeHandle`修改我们的`MyInput`组件：

```
const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});
```

现在，`Form`组件中通过`inputRef.current`只能取到如下数据结构：

```
{
  focus() {
    realInputRef.current.focus();
  },
}
```

就杜绝了**开发者通过ref取到DOM后，执行不该被使用的API，出现ref失控**的情况。

## 总结

正常情况，`Ref`的使用比较少，他是作为**逃生舱**而存在的。

为了防止错用/滥用导致`ref失控`，`React`限制**默认情况下，不能跨组件传递ref**。

为了破除这种限制，可以使用`forwardRef`。

为了减少`ref`对`DOM`的滥用，可以使用`useImperativeHandle`限制`ref`传递的数据结构。
