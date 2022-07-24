---
title: State-Pattern模式（状态模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**状态模式（State Pattern）**：用类来表示状态。这种类型的设计模式属于行为型模式。
## 介绍
- **意图：**用类来表示状态，允许对象在内部状态发生改变时改变它的行为
- **主要解决：**对象的行为依赖于它的状态（属性），并且可以根据它的状态改变而改变它的相关行为。
- **如何解决：**将各种具体的状态类抽象出来。
- **优点：**
 1、封装了转换规则。
 2、枚举可能的状态，在枚举状态之前需要确定状态种类。 
 3、将所有与某个状态有关的行为放到一个类中，并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为。 
4、允许状态转换逻辑与状态对象合成一体，而不是某一个巨大的条件语句块。 
5、可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数。
- **缺点：**
 1、状态模式的使用必然会增加系统类和对象的个数。 
2、状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱。 
3、状态模式对"开闭原则"的支持并不太好，对于可以切换状态的状态模式，增加新的状态类需要修改那些负责状态转换的源代码，否则无法切换到新增状态，而且修改某个状态类的行为也需修改对应类的源代码。
- **使用场景：**
1、行为随状态改变而改变的场景。
 2、条件、分支语句的代替者。
## 示例
##### State.ts 表示金库状态的接口
```
import { Context } from "./Context";

export interface State {
    doClock(context: Context, hour: number): void;
    doUse(context: Context): void;
    doAlarm(context: Context): void;
    doPhone(context: Context): void;
}
```
##### DayState.ts 表示白天状态的类，实现了State的接口
```
import { Context } from "./Context";
import { State } from "./State";
import NightState from "./NightState";

export default class DayState implements State {
    private static singleton: DayState = new DayState();
    public static getInstance(): DayState {
        return this.singleton;
    }
    doClock(context: Context, hour: number): void {
        if (hour < 9 || hour >= 17) {
            context.changeState(NightState.getInstance());
        }
    }
    doUse(context: Context): void {
        context.recordLog("使用金库（白天）");
    }
    doAlarm(context: Context): void {
        context.callSecurityCenter("按下警铃（白天）")
    }
    doPhone(context: Context): void {
        context.callSecurityCenter("正常通话（白天）")
    }

    public toString(): string {
        return "[白天]"
    }
}
```
##### NightState.ts 表示晚上状态的类，实现了State的接口
```
import { Context } from "./Context";
import { State } from "./State";
import DayState from "./DayState";

export default class NightState implements State {
    private static singleton: NightState = new NightState();
    public static getInstance(): NightState {
        return this.singleton;
    }
    doClock(context: Context, hour: number): void {
        if (hour >= 9 && hour < 17) {
            context.changeState(DayState.getInstance())
        }
    }
    doUse(context: Context): void {
        context.recordLog("紧急：晚上使用金库！");
    }
    doAlarm(context: Context): void {
        context.callSecurityCenter("按下警铃（晚上）")
    }
    doPhone(context: Context): void {
        context.callSecurityCenter("晚上的通话录音")
    }

    public toString(): string {
        return "[晚上]"
    }
}
```
##### Context.ts 表示管理金库的状态，并与报警中心联系的接口
```
import { State } from "./State";

export interface Context {
    setClock(hour: number): void;
    changeState(state: State): void;
    callSecurityCenter(msg: string): void;
    recordLog(msg: string): void;
}
```
##### Safe.ts 实现了Context接口
```
import { Context } from "./Context";
import { State } from "./State";
import DayState from "./DayState";

export default class Safe implements Context {
    public state: State = DayState.getInstance();

    private clock: number;
    public setClock(hour: number): void {
        this.clock = hour;
        console.log(`当前时间是${hour}`);
        this.state.doClock(this, this.clock);
    }

    public changeState(state: State): void {
        console.log(`从${this.state}状态变为了${state}状态`);
        this.state = state;
    }
    public callSecurityCenter(msg: string): void {
        console.log(`call!${msg}`);
    }
    public recordLog(msg: string): void {
        console.log(`record....!${msg}`);
    }
}
```
##### index.ts
```
import Safe from "./Safe";

const safe: Safe = new Safe();
safe.setClock(10);
safe.state.doUse(safe);
safe.state.doAlarm(safe);
safe.state.doPhone(safe);


safe.setClock(20);
safe.state.doUse(safe);
safe.state.doAlarm(safe);
safe.state.doPhone(safe);
```
##### result
```
当前时间是10
record....!使用金库（白天）
call!按下警铃（白天）
call!正常通话（白天）
当前时间是20
从[白天]状态变为了[晚上]状态
record....!紧急：晚上使用金库！
call!按下警铃（晚上）
call!晚上的通话录音
```
## 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-43d4a68e88cdf387.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- State（状态）
State定义了根据不同状态进行不同处理的接口，该接口是那些`处理内容依赖于状态的方法的集合`。
- ConcreteState（具体状态）
ConcreteState角色表示各个具体的状态，实现了State的接口。示例中DayState和NightState
- Context（状况、前后关系、上下文）
Context角色持有当前状态的ConcreteState，此外，它还定义了供外部调用者使用State模式的接口。示例中Context和Safe类为该角色
