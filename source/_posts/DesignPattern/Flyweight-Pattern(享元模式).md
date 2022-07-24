---
title: Flyweight-Pattern(享元模式)
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**享元模式（Flyweight Pattern）:**通过尽量共享示例来避免new出实例，以减少内存占用和提高性能。这种类型的设计模式属于结构型模式。
系统只使用少量的对象，而这些对象都很相似，状态变化很小，可以实现对象的多次复用。由于享元模式要求能够共享的对象必须是细粒度对象，因此它又称为轻量级模式，它是一种对象结构型模式。
## 介绍
- **意图：**运用共享技术有效地支持大量细粒度的对象。
- **主要解决：**在有大量对象时，有可能会造成内存溢出，我们把其中共同的部分抽象出来，如果有相同的业务请求，直接返回在内存中已有的对象，避免重新创建。
- **应用实例：** 
1、JAVA 中的 String，如果有则返回，如果没有则创建一个字符串保存在字符串缓存池里面。
 2、数据库的数据池。
- **优点：**
1、极大减少内存中相似或相同对象数量，节约系统资源，提供系统性能
2、提高效率，由于创建对象的数量减少，所以对系统内存的需求也减小，使得速度更快，效率更高。
3、享元模式中的外部状态相对独立，且不影响内部状态
- **缺点：**为了使对象可以共享，需要将享元对象的部分状态外部化，分离内部状态和外部状态，使程序逻辑复杂
- **使用场景：** 1、系统有大量相似对象。 2、需要缓冲池的场景。
>注意事项： 1、注意划分外部状态和内部状态，否则可能会引起线程安全问题。 2、这些类必须有一个工厂对象加以控制。
## 示例
##### Employer.ts 雇员抽象类
```
export default abstract class Employer{
    protected  _company:string = "COMPANY";//公共属性
    protected  _companyAddress:string = "ADDRESS";//公共属性

    public abstract  SetPropertity(name:string, age:string):void;
    public abstract  ShowInformation():void;
}
```
##### CreatEmployer.ts 创建一个具体雇员的类
```
import Employer from "./Employer";

export default class CreatEmployer extends Employer {
    private _name: string; //特有属性
    private _age: string;
    private _id: string = "01";
    constructor(id: string) {
        super();
        this._id = id;
    }
    public SetPropertity(name: string, age: string): void {
        this._age = age;
        this._name = name;
    }
    public ShowInformation(): void {
        console.log(
            `company:${this._company} companyAddress:${this._companyAddress} name:${this._name} id:${this._id}  age:${this._age}`
        );
    }
}

```
##### EmployerFactory.ts 雇员工厂
```
import Employer from "./Employer";
import CreatEmployer from "./CreatEmployer";

export default class EmployerFactory {
    private static emploryerFactory: EmployerFactory;
    /**
     * 获取唯一对象
     */
    public static getEmployerFactory(): EmployerFactory {
        //判断士兵对象是否存在
        if (this.emploryerFactory == null) {
            //不存在就创建新的对象
            this.emploryerFactory = new EmployerFactory();
        }
        //返回对象
        return this.emploryerFactory;
    }

    public GetEmployer(id: string): Employer {
        let employer: Employer = new CreatEmployer(id);
        return employer;
    }
}

```
##### index.ts 
```
import Employer from "./Employer";
import EmployerFactory from "./EmployerFactory";

const employer1: Employer = EmployerFactory.getEmployerFactory().GetEmployer(
    "01"
);
employer1.SetPropertity("jasen", "24");
employer1.ShowInformation();

const employer2: Employer = EmployerFactory.getEmployerFactory().GetEmployer(
    "02"
);
employer2.SetPropertity("tt", "44");
employer2.ShowInformation();

```
##### result
```
company:COMPANY companyAddress:ADDRESS name:jasen id:01  age:24
company:COMPANY companyAddress:ADDRESS name:tt id:02  age:44
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-1c81c144830027ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 角色
- **Flyweight（抽象享元类）**
通常是接口或抽象类，抽象享元类中声明了具体享元类公共方法，这些方法可以向外界提供享元对象的内部数据（内部状态），同时也可以通过这些方法来设置外部数据（外部状态）
- **ConcreteFlyweight（具体享元类）**
继承抽象享元类，在具体享元类中为内部状态提供存储空间。通常可以结合单例模式来设计具体享元类，为每一个具体享元类提供唯一的享元对象
- **UnshareConcreteFlyweight（非分享具体享元类）**
并不是所有的具体享元类都需要被共享，不能被共享的子类可以设计为非共享具体享元类，当需要一个非共享具体享元类的对象时可以直接通过实例化创建
- **FlyweightFactory（享元工厂类）**
创建并管理享元对象，将各种具体享元类存储到一个享元池中，享元池一般为“键值对”集合，可以结合工厂模式进行设计。当用户请求一个具体享元对象时，享元池中如果保存的有就直接返回给用户，如果没有就创建该享元对象返回给用户并存储到享元池中
## 对比
- 享元模式和工厂模式、单例模式
在区分出不同种类的外部状态后，创建新对象时需要选择不同种类的共享对象，这时就可以使用工厂模式来提供共享对象，在共享对象的维护上，经常会采用单例模式来提供单实例的共享对象。

- 享元模式和组合模式
在使用工厂模式来提供共享对象时，比如某些时候共享对象中的某些状态就是对象不需要的，可以引入组合模式来提升自定义共享对象的自由度，对共享对象的组成部分进一步归类、分层，来实现更复杂的多层次对象结构，当然系统也会更难维护。

- 享元模式和策略模式
策略模式中的策略属于一系列功能单一、细粒度的细粒度对象，可以作为目标对象来考虑引入享元模式进行优化，但是前提是这些策略是会被频繁使用的，如果不经常使用，就没有必要了。
