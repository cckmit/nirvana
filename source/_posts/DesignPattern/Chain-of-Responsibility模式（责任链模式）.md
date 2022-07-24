---
title: Chain-of-Responsibility模式（责任链模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**责任链模式（Chain of Responsibility Pattern）**：多个对象组成一条职责链，然后按照它们在职责链上的顺序处理请求。这种模式给予请求的类型，对请求的发送者和接收者进行解耦。这种类型的设计模式属于行为型模式。
在这种模式中，通常每个接收者都包含对另一个接收者的引用。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个接收者，依此类推。
## 介绍
- **意图：**发送者与接收者解耦，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。
- **主要解决：**职责链上的处理者负责处理请求，发送者无须关心请求的处理细节和请求的传递。
- **何时使用：**在处理消息的时候以过滤很多道。
- **如何解决：**拦截的类都实现统一接口。
- **应用实例：**JS 中的事件冒泡
- **优点： **
1、降低耦合度
2、简化了对象。使得对象不需要知道链的结构。
3、增强给对象指派职责的灵活性。通过改变链内的成员或者调动它们的次序，允许动态地新增或者删除责任。 
4、增加新的请求处理类很方便。
- **缺点：**
 1、不能保证请求一定被接收。
 2、系统性能将受到一定影响，而且在进行代码调试时不太方便，可能会造成循环调用。 
3、可能不容易观察运行时的特征，有碍于除错。

## 示例
##### Trouble.ts 表示发生问题的类，带有事件编号
```
export default class Trouble {
    private num: number;//问题编号
    constructor(num: number) {//生产问题
        this.num = num;
    }
    public toString(): string {
        return `[Trouble ${this.num}]`
    }
    public getNum(): number {
        return this.num;
    }
}
```
##### Support.ts 解决问题的抽象类
```
import Trouble from "./Trouble";

export default abstract class Support {
    private name: string;
    private next: Support;
    constructor(name: string) {
        this.name = name;
    }
    public setNext(next: Support): Support {
        this.next = next;
        return next;
    }
    public support(trouble: Trouble): void {
        if (this.resolve(trouble)) {
            this.done(trouble);
        } else if (this.next != null) {
            this.next.support(trouble);
        } else {
            this.fail(trouble);
        }
    }
    public toString(): string {
        return `[${this.name}]`;
    }
    protected abstract resolve(trouble: Trouble): boolean;
    protected done(trouble: Trouble): void {//解决
        console.log(`${trouble.toString()} is resolved by ${this.toString()}`);
    }
    protected fail(trouble: Trouble): void {//未解决
        console.log(`${trouble.toString()} canot be resolved`);
    }
}
```
##### NoSpport.ts 用于解决问题的类（永不解决）
```
import Support from "./Support";
import Trouble from "./Trouble";

export default class NoSupport extends Support {
    constructor(name: string) {
        super(name);
    }
    protected resolve(trouble: Trouble): boolean {//解决问题的方法
        return false;                             //自己不处理
    }
}
```
##### LimitSupport.ts 用于解决问题的类（仅解决编号小于指定值的问题）
```
import Support from "./Support";
import Trouble from "./Trouble";

export default class LimitSupport extends Support {
    private limit: number;
    constructor(name: string, limit: number) {
        super(name);
        this.limit = limit;
    }
    protected resolve(trouble: Trouble): boolean {//解决问题的方法
        if (trouble.getNum() < this.limit) {
            return true;//正常应该为解决问题的方法
        } else {
            return false;                             //自己不处理
        }
    }
}
```
##### OddSupport.ts 用于解决问题的类（仅解决奇数编号的问题）
```
import Support from "./Support";
import Trouble from "./Trouble";
/**
 * 解决奇数问题
 */
export default class OddSupport extends Support {
    constructor(name: string) {
        super(name);
    }

    protected resolve(trouble: Trouble): boolean {//解决问题的方法
        if (trouble.getNum() % 2 == 1) {
            return true;//正常应该为解决问题的方法
        } else {
            return false;                             //自己不处理
        }
    }
}
```
##### SpecialSupport.ts 用于解决问题的类（仅解决指定编号的问题）
```
import Support from "./Support";
import Trouble from "./Trouble";

export default class SpecialSupport extends Support {
    private num: number;
    constructor(name: string, num: number) {
        super(name);
        this.num = num;
    }

    protected resolve(trouble: Trouble): boolean {//解决问题的方法
        if (trouble.getNum() == this.num) {
            return true;//正常应该为解决问题的方法
        } else {
            return false;                             //自己不处理
        }
    }
}
```
##### index.ts 
```
import NoSupport from "./NoSupport";
import Support from "./Support";
import LimitSupport from "./LimitSupport";
import SpecialSupport from "./SpecialSupport";
import OddSupport from "./OddSupport";
import Trouble from "./Trouble";

const alice: Support = new NoSupport("Alice");
const bob: Support = new LimitSupport("Bob", 100);
const charlie: Support = new SpecialSupport("Charlie", 429);
const diana: Support = new LimitSupport("Diana", 200);
const elmo: Support = new OddSupport("Elmo");
const fred: Support = new LimitSupport("Fred", 300);

alice.setNext(bob).setNext(charlie).setNext(diana).setNext(elmo).setNext(fred);
for (let i = 0; i < 500; i += 33) {
    alice.support(new Trouble(i));
}
```
##### result
```
[Trouble 0] is resolved by [Bob]
[Trouble 33] is resolved by [Bob]
[Trouble 66] is resolved by [Bob]
[Trouble 99] is resolved by [Bob]
[Trouble 132] is resolved by [Diana]
[Trouble 165] is resolved by [Diana]
[Trouble 198] is resolved by [Diana]
[Trouble 231] is resolved by [Elmo]
[Trouble 264] is resolved by [Fred]
[Trouble 297] is resolved by [Elmo]
[Trouble 330] canot be resolved
[Trouble 363] is resolved by [Elmo]
[Trouble 396] canot be resolved
[Trouble 429] is resolved by [Charlie]
[Trouble 462] canot be resolved
[Trouble 495] is resolved by [Elmo]
```
## 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-173035b742289356.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- Handler（处理者）
Handler定义了处理请求的具体接口，Handler知道下一个处理者是谁，当无法处理时会把事件转给下一个处理者，当然先一个处理者也是Handler角色
- ConcreteHandler（具体的处理者）
ConcreteHandler是处理请求的具体角色
- Client（请求者）
事件发起者
