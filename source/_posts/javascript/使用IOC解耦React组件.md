---
title: 使用IOC解耦React组件
date: 2021-06-10 16:29:48
categories: 前端
---
`IOC`（控制反转）是一种编程思想，可以解耦组件，提高组件复用性。

本文包括两部分：

1.  介绍`IOC`概念
2.  `IOC`在`React`中的应用

## **IOC是什么**

让我们来看个例子：

我们有个士兵的类，在类内部会实例化一种武器：

```
class Soldier {
  constructor() {
    // 这里我们实例化一把步枪
    this.weapon = new Rifle();
  }
  attack() {
    this.weapon.attack();
  }
}
```

士兵的武器应该是多种多样的，但是在`Soldier`类内部依赖了`Rifle`。

所以当我们想将武器从步枪换为手榴弹时，只能这样改写：

```
// ...
constructor() {
  // 这里我们实例化一把步枪
  // this.weapon = new Rifle();
  // 这里我们实例化一个手榴弹
  this.weapon = new Grenade();
}
// ...
```

理想的状态是：士兵不依赖具体的武器，弹药库里有什么武器，就用什么武器。

在这种情况下，`IOC`作为弹药库，就派上了用场。

让我们来改写代码：

### **第一步：DI（Dependency Injection）**

改写的第一步是使士兵不依赖具体的武器，而是将武器作为依赖注入给士兵：

```
class Soldier {
  // 将武器作为依赖注入
  constructor(weapon) {
    this.weapon = weapon;
  }
  attack() {
    this.weapon.attack();
  }
}
```

我们将武器的实例作为`Soldier`的参数传入，于是可以如下调用：

```
const s1 = new Soldier(new Rifle());
const s2 = new Soldier(new Grenade());
```

这一步被称为`DI`（依赖注入）。

### **第二步：IOC容器**

那么武器从哪儿来呢？接下来来打造我们的武器库：

```
class Armory {
  constructor() {
    this.weapon = null;
  }
  setWeapon(weapon) {
    this.weapon = weapon;
  }
  getWeapon() {
    return this.weapon;
  }
}
```

武器库支持存武器(`setWeapon`)和取武器（`getWeapon`）。

现在，士兵不依赖具体武器，只需要去武器库取武器：

```
const armory1 = new Armory();

class Soldier {
  // 将武器作为依赖注入
  constructor(armory) {
    this.weapon = armory.getWeapon();
  }
  attack() {
    this.weapon.attack();
  }
}
```

改造前的依赖关系：

```士兵 --> 武器```

改造前原先应用（士兵）拥有依赖的完全控制权。

改造后的依赖关系：

```士兵 --> 武器库 <-- 武器```

改造后应用（士兵）与服务提供方（武器）解耦，他们通过`IOC`容器（武器库）联系。

从`Demo`也能看出`IOC`与`DI`的关系：`DI`是实现`IOC`编程思想的一种方式。

除了`DI`外，另一种实现方式是`Dependency Lookup`（依赖查找），简称`DL`。

## **IOC与React**

在`React`中，为组件传递的`props`就是一种`DI`实现。

为了跨层级传递数据，我们常使用`Context API`：

```
function Name() {
  const {name} = useContext(nameContext);
  reutrn <h1>{name}</h1>;
}
```

`context`将依赖提供方（`name`）与依赖使用方（`Name`）隔离，可以看作是一种`IOC`实现。
所以说，合理使用`React`可以充分利用`IOC`的思想解耦代码逻辑。
接下来我们看看专业的`DI`库如何与`React`结合：

## **InversifyJS**

**InversifyJS**[1]是一个强大、轻量的`DI`库。
![](https://upload-images.jianshu.io/upload_images/10024246-062089aa62c329c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
首先我们实现依赖（武器的实现）：
```
// armory.ts
import { injectable } from "inversify";

export interface IArmory<T> {
  attack(): T;
}

@injectable()
export class Armory implements IArmory<string> {
  attack() {
    return "Rifle biubiubiu～";
  }
}
```

通过`inversify`提供的`injectable decorator`标记该`class`是可被注入的。

接下来实现需求方（士兵的实现）：

```
import React from "react";
import { IArmory } from "./armory";

export class Soldier extends React.Component {
  private readonly Armory: IArmory<string>;

  render() {
    return <h1 onClick={() => this.armory.attack()}>I am a soldier</h1>;
  }
}
```

最后实例化`IOC`容器，连接需求方与依赖：

```
import { Container } from "inversify";
import { IArmory, Armory } from "./armory";

// 实例化IOC容器
export const container = new Container();
// 将依赖方注入容器，其中armory为该依赖的ID
container.bind<IArmory<string>>("armory").to(Armory);
```

至此，完成一个`React`组件的简单`IOC`。

业务逻辑的更多依赖都可以通过注入IOC容器来实现解耦。

`Hooks`同样可以通过`inversify`完成`IOC`，参考**Dependency injection in React using InversifyJS. Now with React Hooks**[2]

### **参考资料**

[1]**[InversifyJS]( https://github.com/inversify/InversifyJS)**
[2]**[Dependency injection in React using InversifyJS. Now with React Hooks:](https://itnext.io/dependency-injection-in-react-using-inversifyjs-now-with-react-hooks-64f7f077cde6)**

>本文分享自微信公众号 - 魔术师卡颂（gh_bc4b6cbdf9ad），作者：卡颂
