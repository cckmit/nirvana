---
title: Interpreter-Pattern(解释器模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**解释器模式（Interpreter Pattern）**：提供了评估语言的语法或表达式的方式，它属于行为型模式。这种模式实现了一个表达式接口，该接口解释一个特定的上下文。这种模式被用在 SQL 解析、符号处理引擎等。
## 介绍
- **意图：**给定一个语言，定义它的文法表示，并定义一个解释器，这个解释器使用该标识来解释语言中的句子。
- **主要解决：**对于一些固定文法构建一个解释句子的解释器。
- **何时使用：**如果一种特定类型的问题发生的频率足够高，那么可能就值得将该问题的各个实例表述为一个简单语言中的句子。这样就可以构建一个解释器，该解释器通过解释这些句子来解决该问题。
- **应用实例：**编译器、运算表达式计算。
- **优点：**
1、可扩展性比较好，灵活。
2、增加了新的解释表达式的方式。
3、易于实现简单文法。
- **缺点：**
1、可利用场景比较少。
2、对于复杂的文法比较难维护。
3、解释器模式会引起类膨胀。
4、解释器模式采用递归调用方法。
- **使用场景：**
1、可以将一个需要解释执行的语言中的句子表示为一个抽象语法树。
2、一些重复出现的问题可以用一种简单的语言来进行表达。
3、一个简单语法需要解释的场景。
4、这种模式被用在 SQL 解析、符号处理引擎等。
5、EL表达式式的处理
6、正则表达式解释器
7、数学表达式解析器

> 注意事项：是一种不常用的设计模式 – 用于描述如何构成一个简单的语言解释器，主要用于使用面向对象语言开发的 编译器和解释器设计。
当我们需要开发一种新的语言时，可以考虑使用解释器模式。
 尽量不要使用解释器模式，后期维护会有很大麻烦。
## 示例
>用解释器模式设计一个“韶粵通”公交车卡的读卡器程序。
说明：假如“韶粵通”公交车读卡器可以判断乘客的身份，如果是“韶关”或者“广州”的“老人” “妇女”“儿童”就可以免费乘车，其他人员乘车一次扣 2 元。
设计其文法规则如下。
<expression> ::= <city>的<person>
<city> ::= 韶关|广州
<person> ::= 老人|妇女|儿童
##### Expression.ts //抽象表达式类
```
export interface Expression {
    interpret(info: string): boolean;
}

```
##### TerminalExpression.ts //终结符表达式类
```
import { Expression } from "./Expression";

export default class TerminalExpression implements Expression {
    private set = new Array<string>();
    constructor(data: string[]) {
        for (let i = 0; i < data.length; i++) {
            this.set.push(data[i]);
        }
    }
    public interpret(info: string): boolean {
        if (this.set.indexOf(info) >= 0) {
            return true;
        }
        return false;
    }
}

```
##### AndExpression.ts 非终结符表达式类
```
import { Expression } from "./Expression";

export default class AndExpression implements Expression {
    private city: Expression;
    private person: Expression;
    constructor(city: Expression, person: Expression) {
        this.city = city;
        this.person = person;
    }
    public interpret(info: string): boolean {
        const s = info.split("的");
        return this.city.interpret(s[0]) && this.person.interpret(s[1]);
    }
}

```
##### Context.ts //环境类
```
import TerminalExpression from "./TerminalExpression";
import { Expression } from "./Expression";
import AndExpression from "./AndExpression";

export default class Context {
    private citys = ["韶关", "广州"];
    private persons = ["老人", "妇女", "儿童"];
    private cityPerson: Expression;
    constructor() {
        const city: Expression = new TerminalExpression(this.citys);
        const person: Expression = new TerminalExpression(this.persons);
        this.cityPerson = new AndExpression(city, person);
    }
    public freeRide(info: string): void {
        const ok = this.cityPerson.interpret(info);
        if (ok) {
            console.log("您是" + info + "，您本次乘车免费！");
        } else {
            console.log(info + "，您不是免费人员，本次乘车扣费2元！");
        }
    }
}

```
##### index.ts
```
import Context from "./Context";

const bus: Context = new Context();
bus.freeRide("韶关的老人");
bus.freeRide("韶关的年轻人");
bus.freeRide("广州的妇女");
bus.freeRide("广州的儿童");
bus.freeRide("山东的儿童");

```
##### result
```
您是韶关的老人，您本次乘车免费！
韶关的年轻人，您不是免费人员，本次乘车扣费2元！
您是广州的妇女，您本次乘车免费！
您是广州的儿童，您本次乘车免费！
山东的儿童，您不是免费人员，本次乘车扣费2元！
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-2f43bf4016b5d82f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- **AbstractExpression：**(抽象表达式)
声明一个抽象的解释操作，这个接口被抽象语法树中所有的节点所共享；定义语法树节点的共同接口。
- **TernimalExpression：**（终结符表达式）
一个句子中的每个终结符需要该类的一个实例，它实现与文法中的终结符相关联的解释操作；
- **NonternimalExpression：**（非终结符表达式）
  - 对于文法中的每一条规则都需要一个NonternimalExpression类；
  - 为文法中的的每个符号都维护一个AbstractExpression类型的实例变量；
   - 为文法中的非终结符实现解释操作，在实现时，一般要递归地调用表示文法符号的那些对象的解释操作；
- **Context：**（上下文）
包含解释器之外的一些必要信息；
- **Client：**（请求者）
构建一个需要进行解释操作的文法句子，然后调用解释操作进行解释。
## 执行时序
实际进行解释时，按照以下时序进行的：
1、Client构建一个句子，它是NonterminalExpression和TerminalExpression的实例的一个抽象语法树，然后初始化上下文并调用解释操作；
2、每一非终结符表达式节点定义相应子表达式的解释操作。而各终结符表达式的解释操作构成了递归的基础；
3、每一节点的解释操作用上下文来存储和访问解释器的状态
