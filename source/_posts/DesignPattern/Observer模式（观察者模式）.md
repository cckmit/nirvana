---
title: Observer模式（观察者模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
观察者模式（Observer Pattern）:当观察对象的状态变化时，会自动通知给观察者。观察者模式属于行为型模式。
## 介绍
- **意图：**当被观察对象的状态变化时，所有依赖于它的对象都得到通知并被自动更新。
- **主要解决：**被观察对象状态改变给其他对象通知的问题，而且要考虑到易用和低耦合，保证高度的协作。
- **何时使用：**被观察对象的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。
- **如何解决：**使用面向对象技术，可以将这种依赖关系弱化。
- **优点：**
 1、观察者和被观察者是抽象耦合的。
 2、建立一套触发机制。
- **缺点：** 
1、如果一个被观察者对象有很多的直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间。 
2、如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃。
 3、观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。

*注意事项：1、避免循环引用。2、如果顺序执行，某一观察者错误会导致系统卡壳，一般采用异步方式。*
## 示例
##### Observer.ts 表示观察者的接口
```
import NumberGenerator from "./NumberGenerator";

export interface Observer {
    update(generator: NumberGenerator): void;
}
```
##### NumberGenerator.ts 表示生产数值对象的抽象类 
```
import { Observer } from "./Observer";

export default abstract class NumberGenerator {
    private observers: Array<Observer> = new Array();
    public addObserver(observer: Observer): void {//注册Observe
        this.observers.push(observer);
    }
    public delete(observer: Observer) {//删除Observe
        const index = this.observers.indexOf(observer);
        this.observers.splice(index, 1);
    }
    public notifyObservers() {//通知被Observe对象变化
        this.observers.forEach((observer) => {
            observer.update(this);
        })
    }
    public abstract getNumber(): number;
    public abstract execute(): void;
}
```
##### RandomNumberGenerator.ts 生产随机数的类
```
import NumberGenerator from "./NumberGenerator";

export default class RandomNumberGenerator extends NumberGenerator {
    private num: number;
    public getNumber(): number {
        return this.num;
    }
    public execute(): void {
        for (let i = 0; i < 10; i++) {//变化10次
            this.num = Math.ceil(Math.random() * 30);//生产随机数
            this.notifyObservers();//通知数值变化
        }
    }
}
```
##### DigitObserver.ts 表示以数字形式显示数值的类
```
import { Observer } from "./Observer";
import NumberGenerator from "./NumberGenerator";

export default class DigitObserver implements Observer {
    public update(generator: NumberGenerator) {
        console.log(`DigitObserver:${generator.getNumber()}`)
    }
}
```
##### GraphObserver.ts 表示以简单图形表示数值的类
```
import { Observer } from "./Observer";
import NumberGenerator from "./NumberGenerator";

export default class GraphObserver implements Observer {
    public update(generator: NumberGenerator): void {
        let str = `DigitObserver:`;
        const count: number = generator.getNumber();
        for (let i = 0; i < count; i++) {
            str += "*";
        }
        console.log(str);
    }
}
```
##### IncrementNumberGenerator.ts 自增数据变化
```
import NumberGenerator from "./NumberGenerator"

export default class IncrementNumberGenerator extends NumberGenerator {
    private num: number;//自增开始数据
    private step: number;//每次自增数据
    private end: number;//最大数值
    constructor(num: number, end: number, step: number) {
        super();
        this.num = num;
        this.step = step;
        this.end = end;
    }
    public getNumber(): number {
        return this.num;
    }
    public execute(): void {
        while (this.num < this.end) {
            this.notifyObservers();
            this.num += this.step;
        }
    }

}
```
##### index.ts 
```
import DigitObserver from "./DigitObserver";
import { Observer } from "./Observer";
import GraphObserver from "./GraphObserver";
import IncrementNumberGenerator from "./IncrementNumberGenerator";

const generator: IncrementNumberGenerator = new IncrementNumberGenerator(10, 50, 5);
const observer1: Observer = new DigitObserver();
const observer2: Observer = new GraphObserver();
generator.addObserver(observer1);
generator.addObserver(observer2);
generator.execute();
```
##### result 
```
******IncrementNumberGenerator********
DigitObserver:10
DigitObserver:**********
DigitObserver:15
DigitObserver:***************
DigitObserver:20
DigitObserver:********************
DigitObserver:25
DigitObserver:*************************
DigitObserver:30
DigitObserver:******************************
DigitObserver:35
DigitObserver:***********************************
DigitObserver:40
DigitObserver:****************************************
DigitObserver:45
DigitObserver:*********************************************
******RandomNumberGenerator********
DigitObserver:6
DigitObserver:******
DigitObserver:3
DigitObserver:***
DigitObserver:12
DigitObserver:************
DigitObserver:23
DigitObserver:***********************
DigitObserver:27
DigitObserver:***************************
DigitObserver:3
DigitObserver:***
DigitObserver:30
DigitObserver:******************************
DigitObserver:5
DigitObserver:*****
DigitObserver:29
DigitObserver:*****************************
DigitObserver:26
DigitObserver:**************************
```
## 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-df33cae6f551999c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- Subject（观察对象）
Subject角色表示观察对象，Subject角色定义了注册观察者和删除观察者的方法，此外，它还声明了“获取现在的状态”的方法。在示例中NumberGenerator扮演该角色
- ConcreteSubject（具体的观察对象）
ConcreteSubject角色表示具体的观察者对象，当自身状态变化时，它会通知所以已经注册的Observe角色，示例中RandomNumberGenerator、IncrementNumberGenerator扮演该角色
- Observer（观察者）
Observer角色负责解释来自Subject角色的状态变化的通知，为此，它声明了update方法。示例中Observer接口扮演此角色
- ConcreteObserver（具体的观察者）
ConcreteObserver角色表示具体的Observer。当他的update方法被调用后，它去获取要观察的对象的最新状态，在示例中，由DigitObserver、GraphObserver扮演此角色
