---
title: Factory-Method(工厂模式)
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
### 工厂模式
>这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。
### 介绍
- **意图：**定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
- **何时使用：**我们明确地计划不同条件下创建不同实例时。
- **如何解决：**让其子类实现工厂接口，返回的也是一个抽象的产品。
- **关键代码：**创建过程在其子类执行。
- **优点：**
    >1. 一个调用者想创建一个对象，只要知道其名称就可以了。  
    >2. 扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。 
    >3. 屏蔽产品的具体实现，调用者只关心产品的接口。
- **缺点：**每次增加一个产品时，都需要增加一个具体类和对象实现工厂，使得系统中类的个数成倍增加，在一定程度上增加了系统的复杂度，同时也增加了系统具体类的依赖。
### Product类：
用来表示”产品“的类，声明了use的抽象方法。use的实现交给子类
```
export default abstract class Product {
    public abstract use(): void;
}
```
### Factory类:
使用[Template Method](https://www.jianshu.com/p/61549520cddc)模式，声明了creatProduct（生产产品）和registerProduct（注册产品）的抽象方法。具体实现交给子类
```
import Product from './Product';
export default abstract class Factory {
    public create(owner: String): Product {
        const p: Product = this.creatProduct(owner);
        this.registerProduct(p);
        return p;
    }
    protected abstract creatProduct(owner: String): Product;
    protected abstract registerProduct(Product: Product): void;
}
```
### IDCard类：
```
import Product from './Product';
export default class IDCard extends Product {
    private owner: String;
    constructor(owner: String) {
        super();
        console.log(`制作${owner}的ID卡！`);
        this.owner = owner;
    }
    public use(): void {
        console.log(`使用${this.owner}的ID卡`);
    }
    public getOwner(): String {
        return this.owner;
    }
}
```
### IDCardFactory类：
```
import Factory from './Factory';
import Product from './Product';
import IDCard from './IDCard';
export default class IDCardFactory extends Factory {
    private owners: Array<String> = new Array();
    protected creatProduct(owner: String): Product {
        return new IDCard(owner);
    }
    protected registerProduct(product: Product) {
        //转换product类型为IDCard
        this.owners.push((product as IDCard).getOwner());
    }
    public getOwners(): Array<String> {
        return this.owners;
    }
}
```
### main函数
```
import IDCardFactory from './IDCardFactory';
import Product from './Product';

const factory = new IDCardFactory();
const card1: Product = factory.create("小明");
const card2: Product = factory.create("小红");
const card3: Product = factory.create("小刚");

card1.use();
card2.use();
card3.use();
console.log(factory.getOwners());
```
### 输出：
```
制作小明的ID卡！
制作小红的ID卡！
制作小刚的ID卡！
使用小明的ID卡
使用小红的ID卡
使用小刚的ID卡
[ '小明', '小红', '小刚' ]
```
### 结构：
**Product(产品)：**属于框架这一方，是抽象类，定义在Factory Method模式中生产的那些实例所持有的接口（API），具体由子类ConcreteProduct角色决定。
**Creator(创建者)：**属于框架这一方，负责生产Product角色的抽闲类，具体处理由子类ConcreteCreator角色决定
**ConcreteProduct(具体产品)：**属于具体加工方，决定了具体的产品。
**ConcreteCreator(具体的创造者)：**属于具体加工方，负责生产具体的产品
### 类图：
![Factory Method模式类图](https://upload-images.jianshu.io/upload_images/10024246-378922e9df56e360.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
