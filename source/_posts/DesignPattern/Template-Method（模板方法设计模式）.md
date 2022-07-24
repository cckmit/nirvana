---
title: Template-Method（模板方法设计模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
### 模板方法设计模式
>在模板模式（Template Method）中，一个抽象类公开定义了执行它的方法的方式/模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。这种类型的设计模式属于行为型模式。
### 介绍
- **意图：**定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

- **主要解决：**一些方法通用，却在每一个子类都重新写了这一方法。

- **何时使用：**有一些通用的方法。

- **如何解决：**将这些通用算法抽象出来。

- **关键代码：**在抽象类实现，其他步骤在子类实现。

- **优点：**
>1.  封装不变部分，扩展可变部分。 
>2. 提取公共代码，便于维护。 
>3. 行为由父类控制，子类实现。

- **使用场景：**
> 1. 有多个子类共有的方法，且逻辑相同。 
>2. 重要的、复杂的方法，可以考虑作为模板方法。

### AbstractDisplay类：定义打印逻辑
```
export default abstract class AbstractDisplay {
    public abstract open(): void;
    public abstract print(): void;
    public abstract close(): void;
    public display(): void {
        this.open();
        for (let i = 0; i < 5; i++) {
            this.print();
        }
        this.close();
    }
}
```
### CharDisplay类:打印方式
```
import AbstractDisplay from "./AbstractDisplay";

export default class CharDisplay extends AbstractDisplay {
    private ch: string;

    constructor(ch: string) {
        super();
        this.ch = ch;
    }
    public open() {
        console.log("《");
    }
    public print() {
        console.log(this.ch);
    }
    public close() {
        console.log("》");
    }
}
```
### StringDisplay类：
```
import AbstractDisplay from "./AbstractDisplay";

export default class StringDisplay extends AbstractDisplay {
    private str: string;
    constructor(str: string) {
        super();
        this.str = str;
    }
    public open() {
        console.log("+-----+");
    }
    public print() {
        console.log(`|${this.str}|`);
    }
    public close() {
        console.log("+-----+");
    }
}
```
### main函数
```
import AbstractDisplay from "./AbstractDisplay"
import CharDisplay from "./CharDisplay"
import StringDisplay from "./StringDisplay";

const d1: AbstractDisplay = new CharDisplay("H");
const d2: AbstractDisplay = new StringDisplay("Hello")
d1.display();
d2.display();
```
### 输出
```
《
H
H
H
H
H
》
+-----+
|Hello|
|Hello|
|Hello|
|Hello|
|Hello|
+-----+
```
### 结构
AbstractClass（抽象类）：AbstractClass不仅负责实现模板方法，还负责声明模板方法中所使用的的抽象方法，这些方法在ConcreteClass去实现
ConcreteClass（具体子类）：具体实现AbstractClass类中定义的抽象方法
### 类图
![Template Method类图](https://upload-images.jianshu.io/upload_images/10024246-7f402cd441e847d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
