---
title: Decorator模式（装饰器模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
## 介绍
- 意图：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。
- 主要解决：一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。
- 优点：装饰类和被装饰类可以独立发展，不会相互耦合，装饰模式是继承的一个替代模式，装饰模式可以动态扩展一个实现类的功能。
- 缺点：
1.多层装饰比较复杂。
2.会导致程序中增加很多功能类似的很小的类
- 使用场景： 
1、扩展一个类的功能。 
2、动态增加功能，动态撤销。

*注意事项：可代替继承。*
## 示例
##### Display.ts 用于显示字符串的抽象类
```
export default abstract class Display {
    public abstract getColumns(): number;//获取横向字符数
    public abstract getRows(): number;//获取纵向行数
    public abstract getRowText(row: number): string;//获取第row行的字符串
    /**
      *显示所有字符
    */
    public show(): void {
        for (let i = 0; i < this.getRows(); i++) {
            console.log(this.getRowText(i));
        }
    }
}
```
##### StringDisplay.ts 用于显示单行字符串的类
```
import Display from "./Display";

export default class StringDisplay extends Display {
    private str: string;//要显示的字符串
    constructor(str: string) {
        super();
        this.str = str;
    }

    public getColumns(): number {//字符数
        return this.str.length;
    }
    public getRows(): number {//行数1
        return 1;
    }
    public getRowText(row: number): string {//只显示第一行
        if (row == 0) {
            return this.str;
        } else {
            return null;
        }
    }
}
```
##### Border.ts 用于显示装饰边框的抽象类
```
import Display from "./Display";

export default abstract class Border extends Display {
    protected display: Display;//装饰物
    constructor(display: Display) {
        super();
        this.display = display;
    }
}
```
##### SideBorder.ts 用于显示左右边框的类
```
import Display from "./Display";
import Border from "./Border";

export default class SideBorder extends Border {
    private borderChar: string;//装饰边框的字符
    constructor(display: Display, ch: string) {//指定Display和装饰边框字符
        super(display);
        this.borderChar = ch;
    }
    public getColumns(): number {
        return 1 + this.display.getColumns() + 1;//字符数加上两侧边框的字符数
    }
    public getRows(): number {
        return this.display.getRows();
    }
    public getRowText(row: number) {//字符串加上边框
        return this.borderChar + this.display.getRowText(row) + this.borderChar;
    }
}
```
##### FullBorder.ts  显示上下左右边框
```
import Display from "./Display";
import Border from "./Border";

export default class FullBorder extends Border {
    constructor(display: Display) {
        super(display);
    }
    public getColumns(): number {
        return 1 + this.display.getColumns() + 1;//增加左右边框字符数
    }
    public getRows(): number {
        return this.display.getRows() + 2;//增加上下边框字符数
    }
    public getRowText(row: number) {
        if (row == 0 || row == this.display.getRows() + 1) {//上线边框
            return `+${this.makeLine('-', this.display.getColumns())}+`;
        } else {
            return `|${this.display.getRowText(row - 1)}|`;//左右边框
        }
    }
    private makeLine(ch: string, count: number) {
        let str = "";
        for (let i = 0; i < count; i++) {
            str = str + ch;
        }
        return str;
    }
}
```
##### index.ts 
```
import Display from "./Display";
import StringDisplay from "./StringDisplay";
import SideBorder from "./SideBorder";
import FullBorder from "./FullBorder";

const b1: Display = new StringDisplay("Hello,World.");//直接显示
const b2: Display = new SideBorder(b1, '#');//增加左右边框'#'
const b3: Display = new FullBorder(b2);//上下左右都加边框

b1.show();
b2.show();
b3.show();

const b4: Display = new SideBorder(//多层边框
    new FullBorder(
        new FullBorder(
            new SideBorder(
                new FullBorder(new StringDisplay("Hello,World.")), "*"
            )
        )
    ), "/"
)

b4.show();
```
##### result
```
Hello,World.                       //b1
#Hello,World.#                     //b2   
+--------------+                  //b3
|#Hello,World.#|
+--------------+
/+------------------+/            //b4
/|+----------------+|/
/||*+------------+*||/
/||*|Hello,World.|*||/
/||*+------------+*||/
/|+----------------+|/
/+------------------+/
```
## 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-4099bfc084274a3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- Component
增加功能时的核心角色。示例中Display类扮演该角色
- ConcreteComponent
该角色实现了Component角色定义的接口。在示例中StringDisplay类扮演该角色
- Decorator（装饰物）
该角色具有与Component角色相同的接口，在内部保存了了被装饰的对象->Component。Decorator橘色知道自己要装饰的对象。在示例中，Border类扮演该角色
- ConcreteDecorator（具体的装饰物）
该角色是具体的Decorator角色，示例中SideBorder类和FullBorder类扮演该角色
