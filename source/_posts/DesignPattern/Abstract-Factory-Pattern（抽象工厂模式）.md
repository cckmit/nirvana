---
title: Abstract-Factory-Pattern（抽象工厂模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**抽象工厂模式（Abstract Factory Pattern）**：抽象工厂的工作是将“抽象零件”组装成“抽象产品”。不关心零件的具体实现，而是值关心接口。我们使用该接口将零件组装成产品
在抽象工厂模式中，接口是负责创建一个相关对象的工厂，不需要显式指定它们的类。每个生成的工厂都能按照工厂模式提供对象。
## 介绍
- **意图：**提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
- **主要解决：**主要解决接口选择的问题。
- **何时使用：**系统的产品有多于一个的产品族，而系统只消费其中某一族的产品。
- **如何解决：**在一个产品族里面，定义多个产品。
- **关键代码：**在一个工厂里聚合多个同类产品。
- **优点：**当一个产品族中的多个对象被设计成一起工作时，它能保证客户端始终只使用同一个产品族中的对象。
- **缺点：**产品族扩展非常困难，要增加一个系列的某一产品，既要在抽象的 Creator 里加代码，又要在具体的里面加代码。
- **使用场景：**
 1、QQ 换皮肤，一整套一起换。 
2、生成不同操作系统的程序。
>注意事项：产品族难扩展，产品等级易扩展。

## 示例
Shape.ts 创建一个形状接口
```
export interface Shape {
    draw(): void;
}
```
Square.ts 正方形类实现形状接口
```
import { Shape } from "./Shape";

export default class Square implements Shape {
    public draw(): void {
        console.log("Inside Square::draw() method.");
    }
}
```
Rectangle.ts 长方形类实现形状接口
```
import { Shape } from "./Shape";

export default class Rectangle implements Shape {

    public draw(): void {
        console.log("Inside Rectangle::draw() method.");
    }
}
```
Circle.ts 圆形类实现形状接口
```
import { Shape } from "./Shape";

export default class Circle implements Shape {
    public draw(): void {
        console.log("Inside Circle::draw() method.");
    }
}
```
Color.ts 创建一个颜色接口。
```
export interface Color {
    fill(): void;
}
```
Red.ts 红色类实现颜色接口
```
import { Color } from "./Color";

export default class Red implements Color {

    public fill(): void {
        console.log("Inside Red::fill() method.");
    }
}
```
Blue.ts 蓝色类实现颜色接口
```
import { Color } from "./Color";

export default class Blue implements Color {

    public fill(): void {
        console.log("Inside Blue::fill() method.");
    }
}
```
Green.ts 绿色类实现颜色接口
```
import { Color } from "./Color";

export default class Green implements Color {

    public fill(): void {
        console.log("Inside Green::fill() method.");
    }
}
```
AbstractFactory.ts 抽象工厂，为Color、Shape创建抽象对象
```
import { Color } from "./Color";
import { Shape } from "./Shape";

export default abstract class AbstractFactory {
    public abstract getColor(color: string): Color;
    public abstract getShape(shape: string): Shape;
}
```
ColorFactory.ts 创建扩展了 AbstractFactory 的工厂类，基于给定的信息生成实体类的对象
```
import AbstractFactory from "./AbstractFactory";
import { Shape } from "./Shape";
import { Color } from "./Color";
import Red from "./Red";
import Green from "./Green";
import Blue from "./Blue";

export default class ColorFactory extends AbstractFactory {
    public getShape(shapeType: string): Shape {
        return null;
    }
    public getColor(color: string): Color {
        if (color == null) {
            return null;
        }
        if (color.toUpperCase() === "RED") {
            return new Red();
        } else if (color.toUpperCase() === "GREEN") {
            return new Green();
        } else if (color.toUpperCase() === "BLUE") {
            return new Blue();
        }
        return null;
    }
}
```
ShapeFactory.ts 创建扩展了 AbstractFactory 的工厂类，基于给定的信息生成实体类的对象
```
import AbstractFactory from "./AbstractFactory";
import { Shape } from "./Shape";
import Circle from "./Circle";
import Rectangle from "./Rectangle";
import Square from "./Square";
import { Color } from "./Color";

export default class ShapeFactory extends AbstractFactory {

    public getShape(shapeType: string): Shape {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.toUpperCase() === "CIRCLE") {
            return new Circle();
        } else if (shapeType.toUpperCase() === "RECTANGLE") {
            return new Rectangle();
        } else if (shapeType.toUpperCase() === "SQUARE") {
            return new Square();
        }
        return null;
    }
    public getColor(color: string): Color {
        return null;
    }
}
```
FactoryProducer.ts 创建一个工厂创造器/生成器类，通过传递形状或颜色信息来获取工厂
```
import AbstractFactory from "./AbstractFactory";
import ShapeFactory from "./ShapeFactory";
import ColorFactory from "./ColorFactory";

export default class FactoryProducer {
    public static getFactory(choice: string): AbstractFactory {
        if (choice.toUpperCase() === "SHAPE") {
            return new ShapeFactory();
        } else if (choice.toUpperCase() === "COLOR") {
            return new ColorFactory();
        }
        return null;
    }
}
```
index.ts 
```
import FactoryProducer from "./FactoryProducer";
//获取形状工厂
const shapeFactory = FactoryProducer.getFactory("SHAPE");

//获取形状为 Circle 的对象
const shape1 = shapeFactory.getShape("CIRCLE");

//调用 Circle 的 draw 方法
shape1.draw();

//获取形状为 Rectangle 的对象
const shape2 = shapeFactory.getShape("RECTANGLE");

//调用 Rectangle 的 draw 方法
shape2.draw();

//获取形状为 Square 的对象
const shape3 = shapeFactory.getShape("SQUARE");

//调用 Square 的 draw 方法
shape3.draw();
//获取颜色工厂
const colorFactory = FactoryProducer.getFactory("COLOR");

//获取颜色为 Red 的对象
const color1 = colorFactory.getColor("RED");

//调用 Red 的 fill 方法
color1.fill();

//获取颜色为 Green 的对象
const color2 = colorFactory.getColor("Green");

//调用 Green 的 fill 方法
color2.fill();

//获取颜色为 Blue 的对象
const color3 = colorFactory.getColor("BLUE");

//调用 Blue 的 fill 方法
color3.fill();

```
## result
```
Inside Circle::draw() method.
Inside Rectangle::draw() method.
Inside Square::draw() method.
Inside Red::fill() method.
Inside Green::fill() method.
Inside Blue::fill() method.
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-77c13ebb515b20a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- **AbstractProduct（抽象产品）**
AbstractProduct角色负责定义AbstractFactory角色所生产的抽象零件和产品的接口
- **AbstractFactory（抽象工厂）**
AbstractFactory角色负责定义用于生产抽象产品的接口
- **Client（委托者）**
Client角色仅会调用AbstractFactory角色和AbstractProduct角色的接口进行工作，对于具体的零件、产品和工厂一无所知。
- **ConcreteProduct（具体产品）**
ConcreteProduct角色负责实现AbstractProduct角色定义的接口
- **ConcreteFactory（具体工厂）**
ConcreteFactory角色负责实现AbstractFactory角色的接口。
