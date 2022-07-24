---
title: Mediator模式（中介者模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**中介者模式**（Mediator Pattern）提供了一个中介类，该类通常处理不同类之间的通信，降低多个对象和类之间的通信复杂性，并支持松耦合，使代码易于维护。中介者模式属于行为型模式。
## 介绍
- **意图：**中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。
- **主要解决：**对象与对象之间存在大量的关联关系
- **何时使用：**多个类相互耦合，形成了网状结构。
- **如何解决：**将上述网状结构分离为星型结构。
- **应用实例： **MVC 框架，其中C（控制器）就是 M（模型）和 V（视图）的中介者。
- **优点：**
1、降低了类的复杂度，将一对多转化成了一对一。 
2、各个类之间的解耦。 
3、符合迪米特原则。
- **缺点：**中介者会庞大，变得复杂难以维护。
- **使用场景：**
 1、系统中对象之间存在比较复杂的引用关系，导致它们之间的依赖关系结构混乱而且难以复用该对象。
 2、想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。
*注意事项：不应当在职责混乱的时候使用。*
## 示例
##### SmartDevice.ts 智能设备类(抽象同事角色)
```
import SmartMediator from "./SmartMediator";

export default abstract class SmartDevice {
    //相关设备打开之后 使其进入准备状态
    public abstract readyState(instruction: string): void;
    //操作该设备
    public abstract operateDevice(instruction: string, mediator: SmartMediator): void;
}
```
##### CurtainDevice.ts 窗帘设备(具体同事角色)
```
import SmartDevice from "./SmartDevice";
import SmartMediator from "./SmartMediator";

export default class CurtainDevice extends SmartDevice {

    public operateDevice(instruction: string, mediator: SmartMediator): void {
        console.log(`窗帘已${instruction}`);//通过传入指令，打开或关闭窗帘
        mediator.curtain(instruction);//窗帘通过中介者唤醒音乐设备和洗浴设备
    }

    public readyState(instruction: string): void {
        //如果其他设备开启则调用此方法，唤醒窗帘
        console.log(`窗帘设备准备${instruction}`);
    }

}
```
##### MusicDevice.ts 音响设备(具体同事角色)
```
import SmartDevice from "./SmartDevice";
import SmartMediator from "./SmartMediator";

export default class MusicDevice extends SmartDevice {

    public operateDevice(instruction: string, mediator: SmartMediator): void {
        console.log(`音乐设备已${instruction}`);//通过传入指令，打开或关闭窗帘
        mediator.curtain(instruction);//窗帘通过中介者唤醒音乐设备和洗浴设备
    }

    public readyState(instruction: string): void {
        //如果其他设备开启则调用此方法，唤醒窗帘
        console.log(`音乐设备准备${instruction}`);
    }

}
```
##### BathDevice.ts 洗浴设备(具体同事角色)
```
import SmartDevice from "./SmartDevice";
import SmartMediator from "./SmartMediator";

export default class BathDevice extends SmartDevice {

    public operateDevice(instruction: string, mediator: SmartMediator): void {
        console.log(`洗浴设备${instruction}`);//通过传入指令，打开或关闭窗帘
        mediator.curtain(instruction);//窗帘通过中介者唤醒音乐设备和洗浴设备
    }

    public readyState(instruction: string): void {
        //如果其他设备开启则调用此方法，唤醒窗帘
        console.log(`洗浴设备正在准备${instruction}`);
    }

}
```
##### SmartMediator.ts 抽象中介者
```
import SmartDevice from "./SmartDevice";

export default abstract class SmartMediator {
    //保留所有设备的引用是为了当接收指令时可以唤醒其他设备的操作
    public bd: SmartDevice;
    public md: SmartDevice;
    public cd: SmartDevice;
    constructor(bd: SmartDevice, md: SmartDevice, cd: SmartDevice) {
        this.bd = bd;
        this.md = md;
        this.cd = cd;
    }
    public abstract music(instruction: string): void;
    public abstract curtain(instruction: string): void;
    public abstract bath(instruction: string): void;
}

```
##### Shape.ts 具体中介者
```
import SmartDevice from "./SmartDevice";
import SmartMediator from "./SmartMediator";

export default class ConcreteMediator extends SmartMediator {

    constructor(bd: SmartDevice, cd: SmartDevice, md: SmartDevice) {
        super(bd, cd, md);
    }

    public music(instruction: string): void {//音乐被唤醒后，使其他设备进入准备状态
        this.cd.readyState(instruction);//调用窗帘的准备方法
        this.bd.readyState(instruction);//调用洗浴设备的准备方法
    }

    public curtain(instruction: string): void {
        this.md.readyState(instruction);
        this.bd.readyState(instruction);
    }

    public bath(instruction: string): void {
        this.cd.readyState(instruction);
        this.md.readyState(instruction);
    }

}

```
##### Shape.ts 模型接口
```
export interface Shape {
    draw(): void;
}
```
##### index.ts 
```
import SmartDevice from "./SmartDevice";
import BathDevice from "./BathDevice";
import CurtainDevice from "./CurtainDevice";
import MusicDevice from "./MusicDevice";
import ConcreteMediator from "./ConcreteMediator";
import SmartMediator from "./SmartMediator";

const bd: SmartDevice = new BathDevice();
const cd: SmartDevice = new CurtainDevice();
const md: SmartDevice = new MusicDevice();
const sm: SmartMediator = new ConcreteMediator(bd, cd, md);//把设备引用都保存在调停者中
cd.operateDevice("open", sm); //开启窗帘
md.operateDevice("close", sm);//关闭音乐

```
##### result
```
窗帘已open
窗帘设备准备open
洗浴设备正在准备open
音乐设备已close
窗帘设备准备close
洗浴设备正在准备close
```
## 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-c92933af9be4cd59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 角色
- 抽象中介者（mediator）
定义一个接口用于和对象通信（SmartDevice）
- 具体中介者（concretemediator）
协调各同事对象实现协作，了解维护各个同事
- 抽象同事角色（colleague）
规定了同事的基本类型
- 具体同事角色（concreteColleague）
每个同事都知道中介者对象，要与同事通信则把通信告诉中介者
