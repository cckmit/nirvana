---
title: Singleton模式
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**单例模式（Singleton）**这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。
>注意：
>1、单例类只能有一个实例。
>2、单例类必须自己创建自己的唯一实例。
>3、单例类必须给所有其他对象提供这一实例。
### 介绍
**意图：**保证一个类仅有一个实例，并提供一个访问它的全局访问点。
**主要解决：**一个全局使用的类频繁地创建与销毁。
**何时使用：**当您想控制实例数目，节省系统资源的时候。
**关键代码：**构造函数是私有的。
**优点：**
>1、在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
>2、避免对资源的多重占用（比如写文件操作）。

**缺点：**没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。
***注意事项：getInstance() 方法中需要使用同步锁 synchronized (Singleton.class) 防止多线程同时进入造成 instance 被多次实例化。***
**TicketMaker类：**饿汉模式
```
//饿汉式，从名字上也很好理解，就是“比较勤”，实例在初始化的时候就已经建好了，不管你有没有用到，都先建好了再说。好处是没有线程安全的问题，坏处是浪费内存空间。
export default class TicketMaker {
    private static ticketMaker: TicketMaker = new TicketMaker();
    private ticket: number = 1000;
    private constructor() {
        console.log("初始化TicketMaker");
    }
    public getNextTicketNumber(): number {
        return this.ticket++;
    }
    public static getInstance(): TicketMaker {
        return this.ticketMaker;
    }
}
```
>***注意：***在饿汉模式下，如果通过new生产对象，会出现对象创建2次,可以把constructor函数设置为私有，防止通过new来生产新对象,所有函数的创建通过getInstance实现

**TicketMaker类：**懒汉模式
```
//懒汉式，顾名思义就是实例在用到的时候才去创建，“比较懒”，用的时候才去检查有没有实例，如果有则返回，没有则新建。
export default class TicketMaker {
    private static ticketMaker: TicketMaker;
    private ticket: number = 1000;
    constructor() {
        console.log("初始化TicketMaker");
    }
    public getNextTicketNumber(): number {
        return this.ticket++;
    }
    public static getInstance(): TicketMaker {
        if (!this.ticketMaker) {
            this.ticketMaker = new TicketMaker();
        }
        return this.ticketMaker;
    }
}
```
**main 函数：**
```
import TicketMaker from "./TicketMaker";
const ticketMaker1 = TicketMaker.getInstance();
for (let i = 0; i < 10; i++) {
    console.log(`第${i}个${ticketMaker1.getNextTicketNumber()}`);
}
const ticketMaker2 = TicketMaker.getInstance();
for (let i = 0; i < 10; i++) {
    console.log(`第${i}个${ticketMaker2.getNextTicketNumber()}`);
}
console.log(ticketMaker1 === ticketMaker2);
```
**输出：**
```
初始化TicketMaker
第0个1000
第1个1001
第2个1002
第3个1003
第4个1004
第0个1005
第1个1006
第2个1007
第3个1008
第4个1009
true
```
### 结构：
**Singleton:**Singleton角色有一个返回唯一实例的static方法，改方法总是返回同一实例
### 类图：
![Singleton类图](https://upload-images.jianshu.io/upload_images/5180339-a9aade0286215a70.png?imageMogr2/auto-orient/strip|imageView2/2/w/246/format/webp)
