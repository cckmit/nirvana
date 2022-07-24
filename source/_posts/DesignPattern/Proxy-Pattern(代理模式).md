---
title: Proxy-Pattern(代理模式)
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
代理模式（Proxy Pattern）：代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用。这种类型的设计模式属于结构型模式。
## 介绍
- **意图：**为其他对象提供一种代理以控制对这个对象的访问。
- **主要解决：**不直接操作对象的情况下,对此对象进行访问。
- **何时使用：**想在访问一个类时做一些控制。
- **应用实例：**买火车票不一定在火车站买，也可以去代售点。
- **优点：**
 1、职责清晰。 
2、高扩展性。
 3、智能化。
- **缺点：**
 1、由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。 
2、实现代理模式需要额外的工作，有些代理模式的实现非常复杂。
- **使用场景：**
 1、远程代理。
 2、虚拟代理。 
3、Copy-on-Write 代理。
 4、保护（Protect or Access）代理。
 5、Cache代理。
 6、防火墙（Firewall）代理。 
7、同步化（Synchronization）代理。
 8、智能引用（Smart Reference）代理。
- **注意事项：**
 1、和适配器模式的区别：适配器模式主要改变所考虑对象的接口，而代理模式不能改变所代理类的接口。
 2、和装饰器模式的区别：装饰器模式为了增强功能，而代理模式是为了加以控制。
## 示例
##### BuyHouse.ts 创建服务类接口
```
export interface BuyHouse {
    buyHosue(): void;
}
```
##### BuyHouseImpl.ts 实现服务接口
```
import { BuyHouse } from "./BuyHouse";

export default class BuyHouseImpl implements BuyHouse {

    constructor() {

    }
    public buyHosue(): void {
        console.log("我要买房");
    }
}
```
##### BuyHouseProxy.ts 创建代理类
```
import { BuyHouse } from "./BuyHouse";

export default class BuyHouseProxy implements BuyHouse {
    private buyHouse: BuyHouse;
    constructor(buyHouse: BuyHouse) {
        this.buyHouse = buyHouse;
    }

    public buyHosue(): void {
        console.log("买房前准备");
        this.buyHouse.buyHosue();
        console.log("买房后装修");
    }
}
```
##### index.ts
```
import BuyHouseImpl from "./BuyHouseImpl";
import BuyHouseProxy from "./BuyHouseProxy";

const buyHouse = new BuyHouseImpl();
buyHouse.buyHosue();
const buyHouseProxy = new BuyHouseProxy(buyHouse);
buyHouseProxy.buyHosue();
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-b4f89a6249dd3f0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色：
- Subject(主体)：声明了RealSubject和Proxy的共同接口。由于存在Subject角色，所以Client角色不必在意他所使用的的究竟是RealSubject还是Proxy。
- RealSubject（实际的主体）：也称为委托角色或者被代理角色。定义了代理对象所代表的目标对象。
- Proxy（代理人）：也叫委托类、代理类。Proxy角色处理来自Client的请求，代理对象通常在客户端调用传递给目标对象之前或之后，执行某个操作，而不是单纯地将调用传递给目标对象。
