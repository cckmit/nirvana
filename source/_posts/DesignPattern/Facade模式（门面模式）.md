---
title: Facade模式（门面模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
门面模式（Facade Pattern）：互相关联的复杂类，统一整合出对外部暴露可以访问系统的接口。这种类型的设计模式属于结构型模式，它向现有的系统添加一个接口，来隐藏系统的复杂性。
## 介绍
- **意图：**为子系统中的一组接口提供一个一致的界面，门面模式定义了一个高层接口。
- **主要解决：**降低访问复杂系统的内部子系统时的复杂度，简化客户端与之的接口。
- **何时使用：**
1、客户端不需要知道系统内部的复杂联系，整个系统只需提供一个"接待员"即可。
2、定义系统的入口。
-  **优点：**
1、减少系统相互依赖。 
2、提高灵活性。
3、提高了安全性。
- **缺点：**不符合开闭原则，如果要改东西很麻烦，继承重写都不合适。
- **使用场景：** 
1、为复杂的模块或子系统提供外界访问的模块。
2、子系统相对独立。 
3、预防低水平人员带来的风险。

*注意事项：在层次化结构中，可以使用外观模式定义系统中每一层的入口。*
## 示例
##### Shape.ts 模型接口
```
export interface Shape {
    draw(): void;
}
```
##### Rectangle.ts 矩形类
```
import { Shape } from "./Shape";

export default class Rectangle implements Shape {
    public draw(): void {
        console.log("Rectangle::draw()");
    }
}
```
##### Square.ts 正方形类
```
import { Shape } from "./Shape";

export default class Square implements Shape {

    public draw(): void {
        console.log("Square::draw()");
    }
}
```
##### Circle.ts 圆形类
```
import { Shape } from "./Shape";

export default class Circle implements Shape {

    public draw(): void {
        console.log("Circle::draw()");
    }
}
```
##### ShapeMaker.ts 图形绘制门面类
```
import { Shape } from "./Shape";
import Circle from "./Circle";
import Rectangle from "./Rectangle";
import Square from "./Square";

export default class ShapeMaker {
    private circle: Shape;
    private rectangle: Shape;
    private square: Shape;

    constructor() {
        this.circle = new Circle();
        this.rectangle = new Rectangle();
        this.square = new Square();
    }

    public drawCircle(): void {//绘制圆形
        this.circle.draw();
    }
    public drawRectangle(): void {//绘制矩形
        this.rectangle.draw();
    }
    public drawSquare(): void {//绘制正方形
        this.square.draw();
    }
}
```
##### index.ts 
```
import ShapeMaker from "./ShapeMaker";

const shapeMaker: ShapeMaker = new ShapeMaker();

shapeMaker.drawCircle();
shapeMaker.drawRectangle();
shapeMaker.drawSquare();      
```
##### result
```
Circle::draw()
Rectangle::draw()
Square::draw()
```
## 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-02e9be36dbdd957c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 角色
- Facade （门面）
Facde角色向系统外部提供统一接口。示例中ShapeMaker类扮演该角色
- 其他角色
Facade角色调用这些角色进行工作，但是其他角色各自完成自己的工作，并不知道Facade角色，示例中的Circle、Rectangle、Square扮演该角色
- Client（请求角色）
负责调用Facade角色，并不包括在Facade模式中
