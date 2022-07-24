---
title: Adapter模式（适配器）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
>适配器主要用于接口的转换或者将接口不兼容的类对象组合在一起形成对外统一接口，是一种结构性模式
### Adapter模式有以下两种：
- 类适配器模式（使用继承实现）
- 对象适配器（使用委托的适配器）
### 1、使用继承的适配器
Banner类
```
export default class Banner {
    private info: string;
    constructor(info: string) {
        this.info = info;
    }
    /**
     * ShowWithParen
    */
    public ShowWithParen(): void {
        console.log(`(${this.info})`);
    }
    /**
     * ShowWithAster
     */
    public ShowWithAster(): void {
        console.log(`*${this.info}*`);
    }
}
```
Print接口：声明2个方法
```
export interface Print {
    printWeak: () => void;
    printStrong: () => void;
}
```
PrintBanner类：扮演适配器的角色，继承Banner类，实现Print接口
```
import Banner from "./Banner";
import { Print } from "./Print";
export default class PrintBanner extends Banner implements Print {

    constructor(info: string) {
        super(info);
    }
    /**
     * printWeak
     */
    public printWeak(): void {
        this.ShowWithParen();
    }
    /**
     * printStrong
     */
    public printStrong(): void {
        this.ShowWithAster();
    }
}
```
![使用继承的适配器类图](https://upload-images.jianshu.io/upload_images/10024246-144b9455b75871d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2、使用委托的适配器
PrintAbstractClass类
```
export default abstract class PrintAbstractClass {
    public abstract printWeak(): void;
    public abstract printStrong(): void;
}
```
PrintBannerClass类：由于无法同时继承2个类，PrintBannerClass定义为Banner和PrintAbstractClass的子类
```
import PrintAbstractClass from "./PrintAbstractClass";
import Banner from "./Banner";

export default class PrintBannerClass extends PrintAbstractClass {
    private banner: Banner;
    constructor(info: string) {
        super();
        this.banner = new Banner(info);
    }

    public printWeak(): void {
        this.banner.ShowWithParen();
    }
    public printStrong(): void {
        this.banner.ShowWithAster();
    }
}
```
![对象适配器模式的类图（使用委托）](https://upload-images.jianshu.io/upload_images/10024246-3da680b5999179cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### main函数
```
import PrintBanner from "./PrintBanner"
import { Print } from "./Print";
import PrintBannerClass from "./PrintBannerClass";
//只暴露到外面Print接口，可以多个类实现相关接口，使用时，只要new不同的类，然后调用方法使用统一
const p: Print = new PrintBanner("Hellp");
console.log("---------p-----------------")
p.printWeak();
p.printStrong();
//p2会显示多个方法
const p2: PrintBanner = new PrintBanner("hell2");
console.log("---------p2-----------------")
p2.ShowWithAster();
p2.ShowWithParen();
p2.printStrong();
p2.printWeak();
console.log("---------p3-----------------")
const P3 = new PrintBannerClass("test");
P3.printStrong();
P3.printWeak();
```
>---------p-----------------
>(Hellp)
>\*Hellp*
>---------p2-----------------
>\*hell2*
>(hell2)
>\*hell2*
>(hell2)
>---------p3-----------------
>\*test*
>(test)

