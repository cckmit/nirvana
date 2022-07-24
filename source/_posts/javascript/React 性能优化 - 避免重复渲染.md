---
title: React 性能优化 - 避免重复渲染
date: 2022-03-25 14:32:19
categories: 前端
tags: [react]
---
对于函数组件是否需要再次渲染，可以根据 React.memo 与 React.useMemo 来优化。

## 函数组件优化 - React.memo

### React.memo

`React.memo(ReactNode, [(prevProps, nextProps) => {}])`

*   第一个参数：组件
*   第二个参数【可选】：自定义比较函数。两次的 props 相同的时候返回 true，不同则返回 false。返回 true 会阻止更新，而返回 false 则重新渲染。

如果把组件包装在 `React.memo` 中调用，那么组件在相同 props 的情况下渲染相同的结果，以此通过记忆组件渲染结果的方式来提高组件的性能表现。

`React.memo` 仅检查 props 变更，默认情况下其只会对复杂对象做**浅层对比**，如果想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。

```
const MyComponent = React.memo(function MyComponent(props) {
  /* 使用 props 渲染 */
}, (prevProps, nextProps) => {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
})
```

### 结论

当父组件重新渲染时：

*   子组件未使用 React.memo，不管子组件 props 是什么类型，子组件都会重复渲染；
*   子组件使用 React.memo，并且不传入第二个参数

    *   当子组件的 props 是基础类型，子组件不会重复渲染；
    *   当子组件的 props 是引用类型，如果 props 未使用对应的 hook，那么会重复渲染，并且子组件多一次 diff 计算。如果使用对应的 hook，不会重复渲染；
*   子组件使用 React.memo，并且自定义对比函数，子组件是否重复渲染由自定义函数决定；

### 测试

**背景介绍**

在一个父组件里有两个子组件，当父组件发生重新渲染时，两个子组件在不同的条件控制下是否会重新渲染？

**字段解释**

| 字段名 | 含义 | 测试意义 |
| --- | --- | --- |
| title | 基础类型常量 | 测试基础类型改变对子组件的影响 |
| commonObject | 引用类型常量 | 测试引用类型改变对子组件的影响；测试 hook(useMemo) 定义的引用类型改变对子组件的影响 |
| dataSource | useState 定义的引用类型 | 测试 hook(useState) 定义的引用类型改变对子组件的影响 |
| updateXxxxInfo | 方法 | 测试引用类型改变对子组件的影响；测试 hook(useCallBack) 定义的引用类型改变对子组件的影响 |

**基础代码**

子组件 BaseInfo：

```
const BaseInfo = (props) => {
  console.log('BaseInfo 重新渲染， props：', props)
  const { title, dataSource = {} } = props

  return (
    <Card title={title}>
      <div>姓名：{dataSource.name}</div>
    </Card>
  )
}
```
子组件 OtherInfo：

```
const OtherInfo = (props) => {
  console.log('OtherInfo 重新渲染， props：', props)
  const { title, dataSource } = props

  return (
    <Card title={title}>
      <div>学校：{dataSource.school}</div>
    </Card>
  )
}
```

父组件 FunctionTest：

```
function FunctionTest() {
  const [baseInfo, setBaseInfo] = useState({ name: '混沌' })
  const [otherInfo, setOtherInfo] = useState({ school: '上海大学' })

  return (
    <Space direction="vertical" style={{ width: '100%' }}>
      <Space>
        <Button
          onClick={() => {
            console.log('点击-修改基本信息')
            setBaseInfo({ name: '貔貅' })
          }}
        >修改基本信息</Button>
        <Button
          onClick={() => {
            console.log('点击-修改其他信息')
            setOtherInfo({ school: '北京大学' })
          }}
        >修改其他信息</Button>
      </Space>

      <BaseInfo
        title="基本信息 - 子组件"
        dataSource={baseInfo}
      />
      <OtherInfo
        title="其他信息 - 子组件"
        dataSource={otherInfo}
      />
    </Space>
  )
}
```

#### 测试一：修改子组件 `BaseInfo` 为 React.memo 包裹

```
const BaseInfo = React.memo((props) => {
  console.log('BaseInfo 重新渲染， props：', props)
  const { title, dataSource = {} } = props

  return (
    <Card title={title}>
      <div>姓名：{dataSource.name}</div>
    </Card>
  )
})
```

点击“修改基本信息”后，BaseInfo 与 OtherInfo 全部重新渲染。
点击“修改其他信息”后，OtherInfo 重新渲染，BaseInfo 没有重新渲染。
![](https://upload-images.jianshu.io/upload_images/10024246-a0744d2706b3842e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **结论：**
> 
> *   当 props 是基本类型或 react hook（useState） 定义的引用类型时，使用 React.memo 可以阻止重复渲染。
> *   使用了 React.memo 的 BaseInfo，当在 props 相同时没有重复渲染。

#### 测试二：在测试一的基础上，在父组件 `FunctionTest` 中添加引用类型，并传给两个子组件

**测试2.1: 当引用类型的常量是一个对象/数组时**

```
function FunctionTest() {
  //...
  const commonObject = {}
  //...
  return (
    // ...
    <BaseInfo
      title="基本信息 - 子组件"
      dataSource={baseInfo}
      commonObject={commonObject}
  />
    <OtherInfo
      title="其他信息 - 子组件"
      dataSource={otherInfo}
      commonObject={commonObject}
  />
    // ...
  )
}
```

点击“修改基本信息”或“修改其他信息”，BaseInfo 与 OtherInfo 全部重新渲染。
![](https://upload-images.jianshu.io/upload_images/10024246-1cd35b37f04127b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**测试2.2: 当引用类型的常量是一个方法时：**

```
function FunctionTest() {
  //... 
  const updateBaseInfo = () => {
    console.log('更新基本信息，原数据：', baseInfo)
    setBaseInfo({ name: '饕餮' })
  }

  const updateOtherInfo = () => {
    console.log('更新其他信息，原数据：', otherInfo)
    setOtherInfo({ school: '河南大学' })
  }
  //...

  return (
    //...
      <BaseInfo
        title="基本信息 - 子组件"
        dataSource={baseInfo}
                updateBaseInfo={updateBaseInfo}
      />
      <OtherInfo
        title="其他信息 - 子组件"
        dataSource={otherInfo}
                updateOtherInfo={updateOtherInfo}
      />
    //...
  )
}
```

点击“修改基本信息”或“修改其他信息”，BaseInfo 与 OtherInfo 全部重新渲染。
![](https://upload-images.jianshu.io/upload_images/10024246-6d76c4438f152626.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **结论：**
> 
> *   当 props 包含引用类型时，使用 React.memo 并且不自定义比较函数时不能阻止重复渲染。
> *   无论有没有使用 React.memo 都会重新渲染，此时 BaseInfo 性能不如 OtherInfo， 因为 BaseInfo 多了一次 diff。

#### 测试三：在测试二的基础上，添加 hook

**测试3.1：给 commonObject 添加 useMemo hook**

```const commonObject = useMemo(() => {}, [])```

点击“修改基本信息”后，BaseInfo 与 OtherInfo 全部重新渲染。
点击“修改其他信息”后，OtherInfo 重新渲染，BaseInfo 没有重新渲染。
![](https://upload-images.jianshu.io/upload_images/10024246-e62a8a39a67f4962.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**测试3.2: 给 `updateBaseInfo` 与 `updateOtherInfo` 添加 useCallback hook**

```
const updateBaseInfo = useCallback(() => {
  console.log('更新基本信息，原数据：', baseInfo)
  setBaseInfo({ name: '饕餮' })
}, [])

const updateOtherInfo = useCallback(() => {
  console.log('更新其他信息，原数据：', otherInfo)
  setOtherInfo({ school: '河南大学' })
}, [])
```

点击“修改基本信息”后，BaseInfo 与 OtherInfo 全部重新渲染。
点击“修改其他信息”后，OtherInfo 重新渲染，BaseInfo 没有重新渲染。
[图片上传失败...(image-8a8170-1648218334414)] 

> **结论：**
> 
> *   当 props 的函数使用 useMemo/useCallback 时，使用 React.memo 并且不自定义比较函数时可以阻止重复渲染。
> *   使用了 React.memo 的 BaseInfo，当在 props 相同时没有重复渲染。

#### 测试四：在测试三的基础上，给 OtherInfo 添加 React.memo 并且自定义比较函数

```
const OtherInfo = React.memo((props) => {
  console.log('OtherInfo 重新渲染， props：', props)
  const { title, dataSource, updateOtherInfo } = props
  return (
    <Card title={title}>
      <div>学校：{dataSource.school}</div>
      <Button onClick={updateOtherInfo}>更新学校</Button>
    </Card>
  )
}, (prevProps, nextProps) => {
  console.log('OtherInfo props 比较')
  console.log('OtherInfo 老的props：', prevProps)
  console.log('OtherInfo 新的props：', nextProps)
  let flag = true
  Object.keys(nextProps).forEach(key => {
    let result = nextProps[key] === prevProps[key]
    console.log(`比较 ${key}, 结果是：${result}`)
    if (!result) {
      flag = result
    }
  })
  console.log(`OtherInfo 组件${flag ? '不会' : '会'}渲染`)
  return flag
})
```

点击“修改基本信息”后，BaseInfo 重新渲染, OtherInfo 没有重新渲染。
点击“修改其他信息”后，BaseInfo 没有重新渲染，OtherInfo 重新渲染。
![](https://upload-images.jianshu.io/upload_images/10024246-6a00afc9eda50dfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **结论：**
> 
> *   当 props 的函数使用 useMemo/useCallback 时，使用 React.memo 并且不自定义比较函数时可以阻止重复渲染。
> *   React.memo 的第二个参数可以判断是否需要自定义渲染。

## 函数组件优化 - React.useMemo

### React.useMemo

`React.useMemo(() => {}, [])`

返回一个 [memoized](https://link.segmentfault.com/?enc=I7PD97wFWwq2I%2BosigJxkA%3D%3D.%2BbhN13uv06cvAdAhKcvOmbm0YSXMunAcyerw%2B9SEF1pzG6KNuHCa6qMpNOYnDumA) 值。

把“创建”函数和依赖项数组作为参数传入 `useMemo`，它仅会在某个依赖项改变时才重新计算 memoized 值。
如果没有提供依赖项数组，`useMemo` 在每次渲染时都会计算新的值。

### 结论

当父组件重新渲染时：

*   子组件未使用 React.useMemo，不管子组件 props 是什么类型，子组件都会重复渲染；
*   子组件使用 React.useMemo，依赖项数组的值有改变时会造成子组件重复渲染；

### 测试

React.memo 默认是对 props 浅比较，React.useMemo 是对依赖项数组浅比较，所以针对不同的参数比较结果相同【这里就不详细介绍了】。

> 引用类型的参数建议使用 useState, useMemo,useCallback 等hooks，否则浅比较结果不同。

>原文：https://segmentfault.com/a/1190000041011894

