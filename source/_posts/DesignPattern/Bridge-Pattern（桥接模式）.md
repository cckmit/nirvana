---
title: Bridge-Pattern（桥接模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**桥接模式（Bridge Patten）：**：桥接模式是在类的抽象化和实现化之间搭建桥梁，来实现二者的解耦。。这种类型的设计模式属于结构型模式。
## 介绍
- **意图：**将抽象部分与实现部分分离，使它们都可以独立的变化。
- **主要解决：**在有多种可能会变化的情况下，用继承会造成类爆炸问题，扩展起来不灵活。
- **何时使用：**实现系统可能有多个角度分类，每一种角度都可能变化。
- **如何解决：**把这种多角度分类分离出来，让它们独立变化，减少它们之间耦合。
- **关键代码：**抽象类依赖实现类。
- **应用实例：**墙上的开关，可以看到的开关是抽象的，不用管里面具体怎么实现的。
- **优点：** 
1、抽象和实现的分离。 
2、优秀的扩展能力。 
3、实现细节对客户透明。
- **缺点：**桥接模式的引入会增加系统的理解与设计难度，由于聚合关联关系建立在抽象层，要求开发者针对抽象进行设计与编程。
- **使用场景：**
1、对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。 
2、一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。

>注意事项：对于两个独立变化的维度，使用桥接模式再适合不过了。
## 示例
##### Display.ts 负责“显示”的类
```
import DisplayImpl from "./DisplayImpl";

export default class Display {
    private impl: DisplayImpl;
    constructor(impl: DisplayImpl) {
        this.impl = impl;
    }
    public open(): void {
        this.impl.rawOpen();
    }
    public print(): void {
        this.impl.rawPrint();
    }
    public close(): void {
        this.impl.rawClose();
    }
    public display(): void {
        this.open();
        this.print();
        this.close();
    }
}
```
##### CountDisplay.ts 增加了“只显示规定次数”这一功能的类
```
import Display from "./Display";
import DisplayImpl from "./DisplayImpl";

export default class CountDisplay extends Display {
    constructor(impl: DisplayImpl) {
        super(impl);
    }
    public multiDisplay(times: number) {
        this.open();
        for (let i = 0; i < times; i++) {
            this.print();
        }
        this.close();
    }
}
```
##### DisplayImpl.ts 负责“显示”的类
```
export default abstract class DisplayImpl {
    abstract rawOpen(): void;
    abstract rawPrint(): void;
    abstract rawClose(): void;
}
```
##### StringDisplayImpl.ts “用字符串显示”的类
```
import DisplayImpl from "./DisplayImpl";

export default class StringDisplayImpl extends DisplayImpl {
    private str: string;
    private width: number;
    constructor(str: string) {
        super();
        this.str = str;
        this.width = str.length;
    }
    public rawOpen(): void {
        this.printLine();
    }
    public rawPrint(): void {
        console.log(`|${this.str}|`);
    }
    public rawClose(): void {
        this.printLine();
    }
    private printLine(): void {
        let str = '+';
        for (let i = 0; i < this.width; i++) {
            str = str + '-';
        }
        console.log(`${str}+`);
    }
}
```
##### index.ts 
```
import Display from "./Display";
import StringDisplayImpl from "./StringDisplayImpl";
import CountDisplay from "./CountDisplay";

const d1 = new Display(new StringDisplayImpl("Hello,China."));
const d2 = new CountDisplay(new StringDisplayImpl("Hello,World."));
const d3 = new CountDisplay(new StringDisplayImpl("Hello,Universe."));
console.log("-------d1.display()--------")
d1.display();
console.log("-------d2.display()--------")
d2.display();
console.log("-------d3.display()--------")
d3.display();
console.log("-------d4.display()--------")
d3.multiDisplay(5);
```
##### result
```
-------d1.display()--------
+------------+
|Hello,China.|
+------------+
-------d2.display()--------
+------------+
|Hello,World.|
+------------+
-------d3.display()--------
+---------------+
|Hello,Universe.|
+---------------+
-------d3.multiDisplay()--------
+---------------+
|Hello,Universe.|
|Hello,Universe.|
|Hello,Universe.|
|Hello,Universe.|
|Hello,Universe.|
+---------------+
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-9af2691e6e770c44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 角色
- Abstraction（抽象化）
该角色位于“类的功能层次结构”的最上层，它使用Implement角色的方法定义了基本功能。该角色中保存了Implement角色的示例。在示例中，由Display类扮演该角色
- RefinedAbstract（改善后的抽象化）
在Abstraction角色的基础上增加了新的角色。在示例中，由CountDisplay类扮演该角色
- Implementor（实现者）
该角色位于“类的实现层次结构”的最上层。它定义了用于实现Abstraction角色的接口方法。在示例中，由DisplayImpl类扮演此角色
- ConcreteImplementor（具体实现者）
该角色负责实现在Implement角色中定义的接口。在示例中，由StringDisplayImpl类扮演该角色
