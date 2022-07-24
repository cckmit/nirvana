---
title: Command模式（命令模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
命令模式（Command Pattern）是一种数据驱动的设计模式。Command有时也被成为事件（event）。它与“事件驱动编程”中的事件是一样的意思。
## 介绍
- 意图：将一个请求封装成一个对象，从而使您可以用不同的请求对客户进行参数化。
- 主要解决：在软件系统中，行为请求者与行为实现者通常是一种紧耦合的关系，但某些场合，比如需要对行为进行记录、撤销或重做、事务等处理时，这种无法抵御变化的紧耦合的设计就不太合适。
- 何时使用：在某些场合，比如要对行为进行"记录、撤销/重做、事务"等处理，这种无法抵御变化的紧耦合是不合适的。在这种情况下，如何将"行为请求者"与"行为实现者"解耦？将一组行为抽象为对象，可以实现二者之间的松耦合。
- 如何解决：通过调用者调用接受者执行命令，顺序：调用者→命令→接受者。
- 关键代码：定义三个角色：
1、received 真正的命令执行对象 
2、Command 
3、invoker 使用命令对象的入口
- 优点： 1、降低了系统耦合度。 2、新的命令可以很容易添加到系统中去。
- 缺点：使用命令模式可能会导致某些系统有过多的具体命令类。
- 注意事项：系统需要支持命令的撤销(Undo)操作和恢复(Redo)操作，也可以考虑使用命令模式，见命令模式的扩展。
- 命令模式结构示意图:

![类图](https://upload-images.jianshu.io/upload_images/10024246-ba053c9ac15c7266.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 示例
##### order.ts command接口
```
export interface Order {
    execute(): void;
}
```
##### Stock.ts 请求类
```
export default class Stock {
    private name: string = "ABC";
    private quantity: number = 10;
    public buy(): void {
        console.log(`Stock [ Name: ${this.name} Quantity: ${this.quantity} ] bought`);
    }
    public sell(): void {
        console.log(`Stock [ Name: ${this.name} Quantity: ${this.quantity} ] sold`);
    }
}
```
##### 买卖股票实现了 Order 接口
* BuyStock.ts 买股票*
```
//Order.ts 买股票
import { Order } from "./Order";
import Stock from "./Stock";

export default class BuyStock implements Order {
    private abcStock: Stock;

    constructor(abcStock: Stock) {
        this.abcStock = abcStock;
    }

    public execute(): void {
        this.abcStock.buy();
    }
}
```
*SellStock.ts卖股票*
```
//SellStock.ts卖股票
import { Order } from "./Order";
import Stock from "./Stock";

export default class SellStock implements Order {
    private abcStock: Stock;

    constructor(abcStock: Stock) {
        this.abcStock = abcStock;
    }

    public execute(): void {
        this.abcStock.sell();
    }
}
```
##### Broker.ts 经纪人创建命令
```
import { Order } from "./Order";

export default class Broker {
    private orderList: Array<Order> = new Array<Order>();

    public takeOrder(order: Order): void {
        this.orderList.push(order);
    }

    public placeOrders(): void {
        this.orderList.forEach((order: Order) => {
            order.execute();
        })
        this.orderList = [];
    }
}
```
##### index.ts 
```
import Broker from "./Broker";
import Stock from "./Stock";
import BuyStock from "./BuyStock";
import SellStock from "./SellStock";

const abcStock: Stock = new Stock();

const buyStockOrder: BuyStock = new BuyStock(abcStock);
const sellStockOrder: SellStock = new SellStock(abcStock);

const broker: Broker = new Broker();
broker.takeOrder(buyStockOrder);
broker.takeOrder(sellStockOrder);

broker.placeOrders();
```
##### result
```
Stock [ Name: ABC Quantity: 10 ] bought
Stock [ Name: ABC Quantity: 10 ] sold
```
## Command模式中的角色
- Command(命令)
Command角色负责定义命令的接口，对应Order接口
- ConcreteCommand （具体的命令）
ConcreteCommand角色负责试下在Command角色中定义的接口。对应BuyStock和SellStock
- Receiver（接收者）
Receiver角色时Command角色执行命令接受者
- Client（请求者）
Client角色负责生产ConcreteCommand角色并分配Receiver角色
- Invoke（发动者）
Invoke角色时开始执行命令的角色，它会调用Command角色中定义的接口
