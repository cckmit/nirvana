---
title: Visitor-Pattern(访问者模式)
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**访问者模式（Visitor Pattern）**访问者模式是一种将数据操作和数据结构分离的设计模式。这种类型的设计模式属于行为型模式。根据模式，元素对象已接受访问者对象，这样访问者对象就可以处理元素对象上的操作。

## 介绍
- **意图：**主要将数据结构与数据操作分离。
- **主要解决：**稳定的数据结构和易变的操作耦合问题。
- **何时使用：**需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，使用访问者模式将这些封装到类中。
- **如何解决：**在被访问的类里面加一个对外提供接待访问者的接口。
- **关键代码：**在数据基础类里面有一个方法接受访问者，将自身引用传入访问者。
- **优点：**
 1、符合单一职责原则。 
2、优秀的扩展性。
 3、灵活性。
- **缺点：**
 1、具体元素对访问者公布细节，违反了迪米特原则。 
2、具体元素变更比较困难。 
3、违反了依赖倒置原则，依赖了具体类，没有依赖抽象。
- **使用场景：**
 1、对象结构中对象对应的类很少改变，但经常需要在此对象结构上定义新的操作。 
2、需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，也不希望在增加新操作时修改这些类。
>注意事项：访问者可以对功能进行统一，可以做报表、UI、拦截器与过滤器。

## 示例
##### ComputerPart.ts 表示元素的接口
```
import { ComputerPartVisitor } from "./ComputerPartVisitor";

export interface ComputerPart {
    accept(computerPartVisitor: ComputerPartVisitor): void;
}
```
##### Keyboard.ts 键盘实体类
```
import { ComputerPartVisitor } from "./ComputerPartVisitor";
import { ComputerPart } from "./ComputerPart";

export default class Keyboard implements ComputerPart {
    public name: string;
    public accept(computerPartVisitor: ComputerPartVisitor): void {
        this.name = "Keyboard";
        computerPartVisitor.visit(this);
    }
}
```
##### Mouse.ts 鼠标实体类
```
import { ComputerPartVisitor } from "./ComputerPartVisitor";
import { ComputerPart } from "./ComputerPart";

export default class Mouse implements ComputerPart {
    public name: string;
    public accept(computerPartVisitor: ComputerPartVisitor): void {
        this.name = "Mouse";
        computerPartVisitor.visit(this);
    }
}
```
##### Monitor.ts 显示器实体类
```
import { ComputerPartVisitor } from "./ComputerPartVisitor";
import { ComputerPart } from "./ComputerPart";

export default class Monitor implements ComputerPart {
    public name: string;
    public accept(computerPartVisitor: ComputerPartVisitor): void {
        this.name = "Monitor";
        computerPartVisitor.visit(this);
    }
}
```
##### Computer.ts 计算机实体类
```
import { ComputerPartVisitor } from "./ComputerPartVisitor";
import Mouse from "./Mouse";
import Keyboard from "./Keyboard";
import Monitor from "./Monitor";
import { ComputerPart } from "./ComputerPart";

export default class Computer implements ComputerPart {

    private parts: ComputerPart[];
    public name: string;
    constructor() {
        this.parts = [new Mouse(), new Keyboard(), new Monitor()];
        this.name = "Computer";
    }


    public accept(computerPartVisitor: ComputerPartVisitor): void {
        for (let i = 0; i < this.parts.length; i++) {
            this.parts[i].accept(computerPartVisitor);
        }
        computerPartVisitor.visit(this);
    }
}
```
##### ComputerPartVisitor.ts 访问者的接口
```
import Computer from "./Computer";
import Mouse from "./Mouse";
import Keyboard from "./Keyboard";
import Monitor from "./Monitor";

export interface ComputerPartVisitor {
    visit(obj: Computer | Mouse | Keyboard | Monitor): void;
}
```
##### ComputerPartDisplayVisitor.ts 创建实现了上述类的实体访问者。
```
import { ComputerPartVisitor } from "./ComputerPartVisitor";
import Computer from "./Computer";
import Mouse from "./Mouse";
import Keyboard from "./Keyboard";
import Monitor from "./Monitor";

export default class ComputerPartDisplayVisitor implements ComputerPartVisitor {
    public visit(obj: Computer | Mouse | Keyboard | Monitor): void {//JavaScript中没有重载
        console.log(obj.name)
        if (obj.name === "Computer") {
            console.log("Displaying Computer.");

        } else if (obj.name === "Mouse") {

            console.log("Displaying Mouse.");
        } else if (obj.name === "Keyboard") {

            console.log("Displaying Keyboard.");
        } else {
            console.log("Displaying Monitor.");

        }
    }
}
```
##### index.ts
```
import { ComputerPart } from "./ComputerPart";
import Computer from "./Computer";
import ComputerPartDisplayVisitor from "./ComputerPartDisplayVisitor";

const computer: ComputerPart = new Computer();
computer.accept(new ComputerPartDisplayVisitor());
```
##### result
```
Mouse
Displaying Mouse.
Keyboard
Displaying Keyboard.
Monitor
Displaying Monitor.
Computer
Displaying Computer.
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-23af8b73c70c1911.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 角色
- Visitor(访问者)
Visitor角色负责对数据结构中每个具体的元素声明一个访问的visit方法，visit方法是用来操作元素的方法，负责实现该方法的是concreteVisitor角色
- ConcreteVisitor（具体的访问者）
ConcreteVisitor角色负责实现Visitor角色定影的接口，它要实现所有的visit方法，即实现如何处理每个ConcreteElement角色。ConcreteVisitor角色的内部状态会随着visit的变化而变化
- Element（元素）
Element角色表示Visitor角色的访问对象。它声明了接受访问者的accept方法。accept方法接受的参数是Visitor角色。
- ConcreteElement
ConcreteElement负责实现Element角色定义的接口
- ObjectStructure（对象结构）
ObjectStructure角色负责吃力Element角色的集合。ConcreteVisitor角色为每个Element都准备了处理方法。
