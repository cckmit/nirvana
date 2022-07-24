---
title: Builder-Pattern（建造者模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**建造者模式（Builder Pattern）：**组装具有复杂结构的实例。这种类型的设计模式属于创建型模式。
## 介绍
- **意图：**将一个复杂的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。
- **主要解决：**主要解决在软件系统中，有时候面临着"一个复杂对象"的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在一起的算法却相对稳定。
- **何时使用：**一些基本部件不会变，而其组合经常变化的时候。
- **应用实例：** 去肯德基，汉堡、可乐、薯条、炸鸡翅等是不变的，而其组合是经常变化的，生成出所谓的"套餐"。
- **优点：**
 1、建造者独立，易扩展。
 2、便于控制细节风险。
- **缺点：**
 1、产品必须有共同点，范围有限制。 
2、如内部变化复杂，会有很多的建造类。
- **使用场景：**
 1、需要生成的对象具有复杂的内部结构。 
2、需要生成的对象内部属性本身相互依赖。

>注意事项：与工厂模式的区别是：建造者模式更加关注与零件装配的顺序。

## 示例
type.ts 定义食物接口和包装接口
```
/**
 * 食物接口
 */
export interface Food {
    name(): string;
    packing(): Packing;
    price(): number;
}
/**
 * 包装接口
 */
export interface Packing {
    pack(): string
}
```
Wrapper.ts 纸质包装类
```
import { Packing } from "./type";

export default class Wrapper implements Packing {
    public pack(): string {
        return "Wrapper";
    }
}
```
Bottle.ts 定义瓶装类
```
import { Packing } from "./type";

export default class Bottle implements Packing {
    public pack(): string {
        return "Bottle";
    }
}
```
Burger.ts 实现食物接口没定义包装为纸质
```
import { Food, Packing } from "./type";
import Wrapper from "./Wrapper";

export default abstract class Burger implements Food {
    public abstract name(): string;
    public packing(): Packing {
        return new Wrapper();
    }

    public abstract price(): number;
}
```
ColdDrink.ts 冷饮类实现食物接口，并指定包装为瓶装
```
import { Food, Packing } from "./type";
import Bottle from "./Bottle";

export default abstract class ColdDrink implements Food {
    public abstract name(): string;
    public packing(): Packing {
        return new Bottle();
    }

    public abstract price(): number;
}
```
VegBurger.ts 蔬菜汉堡类
```
import Burger from "./Burger";

export default class VegBurger extends Burger {
    public price(): number {
        return 25;
    }
    public name(): string {
        return "Veg Burger";
    }
}
```
ChickenBurger.ts 鸡肉堡类
```
import Burger from "./Burger";

export default class ChickenBurger extends Burger {

    public price(): number {
        return 50.5;
    }

    public name(): string {
        return "Chicken Burger";
    }
}
```
Pepsi.ts 百事饮料类
```
import ColdDrink from "./ColdDrink";

export default class Pepsi extends ColdDrink {

    public price(): number {
        return 35.0;
    }

    public name(): string {
        return "Pepsi";
    }
}
```
Coke.ts 可口可乐类继承自冷饮
```
import ColdDrink from "./ColdDrink";

export default class Coke extends ColdDrink {

    public price(): number {
        return 30.0;
    }
    public name(): string {
        return "Coke";
    }
}
```
Meal.ts 套餐类，里面有食物和总价
```
import { Food } from "./type";

export default class Meal {
    private items = new Array<Food>();

    public addItem(item: Food): void {
        this.items.push(item);
    }

    public getCost(): number {
        let cost = 0;
        for (const item of this.items) {
            cost += item.price();
        }
        return cost;
    }

    public showItems(): void {
        for (const item of this.items) {
            console.log(`Food : ${item.name()}, Packing : ${item.packing().pack()}, Price : ${item.price()}`);
        }
    }
}
```
MealBuilder.ts 创建套餐，有蔬菜和没蔬菜的套餐
```
import Meal from "./Meal";
import VegBurger from "./VegBurger";
import Coke from "./Coke";
import ChickenBurger from "./ChickenBurger";
import Pepsi from "./Pepsi";

export default class MealBuilder {

    public prepareVegMeal(): Meal {
        const meal = new Meal();
        meal.addItem(new VegBurger());
        meal.addItem(new Coke());
        return meal;
    }

    public prepareNonVegMeal(): Meal {
        const meal = new Meal();
        meal.addItem(new ChickenBurger());
        meal.addItem(new Pepsi());
        return meal;
    }
}
```
index.ts 
```
import MealBuilder from "./MealBuilder";

const mealBuilder = new MealBuilder();

const vegMeal = mealBuilder.prepareVegMeal();
console.log("-----Veg Meal-------");
vegMeal.showItems();
console.log("Total Cost: " + vegMeal.getCost());

const nonVegMeal = mealBuilder.prepareNonVegMeal();
console.log("-----Non-Veg Meal-----");
nonVegMeal.showItems();
console.log("Total Cost: " + nonVegMeal.getCost());
```
result
```
-----Veg Meal-------
Food : Veg Burger, Packing : Wrapper, Price : 25
Food : Coke, Packing : Bottle, Price : 30
Total Cost: 55
-----Non-Veg Meal-----
Food : Chicken Burger, Packing : Wrapper, Price : 50.5
Food : Pepsi, Packing : Bottle, Price : 35
Total Cost: 85.5
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-b5a8ab23b145195d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- Builder（建造者）
Builder角色负责定义用于生产实例的接口，Builder角色中有用于生产实例的方法
- ConcreteBuilder(具体的建造者)
ConcreteBuilder角色时负责实现Builder角色的接口的类，定义了在生产实例时实际被调用的犯法，此外，在ConcreteBuilder中还定义了获取最终生产结果的方法
- Director（监工）
Director角色负责使用Builder角色的接口来生产实例，不依赖于ConcreteBuilder角色。它只调用在Builder角色中被定义的方法
- Client（使用者）
该角色使用了Builder模式。
