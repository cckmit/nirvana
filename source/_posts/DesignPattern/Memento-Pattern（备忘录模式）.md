---
title: Memento-Pattern（备忘录模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
## 备忘录模式
备忘录模式（Memento Pattern）保存一个对象的某个状态，以便在适当的时候恢复对象。备忘录模式属于行为型模式。
## 使用场景
使用Memento模式可以实现应用程序的以下功能
- Undo（撤销）
- Redo（重做）
- History（历史记录）
- Snapshot（快照）
## 实现
手机水果和获取金钱数的掷骰子游戏
### Memento类：表示主人公
```
/**
 * Memento类，主人公
 */
export default class Memento {
    public money: number;//金钱
    public fruits: Array<string>;//水果
    /**
     * 获取当前金钱
     */
    public getMoney(): number {
        return this.money;
    }
    constructor(money: number) {
        this.money = money;
        this.fruits = new Array();
    }
    /**
     * 添加水果
     * @param fruit 水果
     */
    public addFruit(fruit: string) {
        this.fruits.push(fruit);
    }
    /**
     * 获取当前水果
     */
    public getFruits(): Array<string> {
        return JSON.parse(JSON.stringify(this.fruits));
    }
}
```
Gamer类：表示游戏主人公的类
```
import Memento from "./Memento";

export default class Gamer {
    private money: number;//所持金钱
    private fruits: Array<string> = new Array();//获得的水果
    private fruitname: string[] = ["苹果", "葡萄", "香蕉", "橘子"];//水果种类
    constructor(money: number) {//构造函数
        this.money = money;
    }
    /**
     * 获取当前所持金钱
     */
    public getMoney(): number {
        return this.money;
    }
    /**
     * 掷骰子游戏
     */
    public bet(): void {
        const dice = Math.ceil(Math.random() * 6);//骰子随机数
        if (dice == 1) {//1金钱+100
            this.money += 100;
            console.log("所持金钱增加了。")
        } else if (dice == 2) {//2金钱减半
            this.money /= 2;
            console.log("所持金钱减半");
        } else if (dice == 6) {//6获取水果
            const f = this.getFruit();
            console.log(`获得了水果（${f}）。`);
            this.fruits.push(f)
        } else {//其他没奖励
            console.log("什么都没有发生。")
        }
    }
    /**
     * 创建快照
     */
    public createMemento(): Memento {
        const m: Memento = new Memento(this.money);
        this.fruits.forEach((item) => {
            if (item.indexOf("好吃的") == 0) {//只保存还吃的水果
                m.addFruit(item)
            }
        })
        return m;
    }
    /**
     * 撤销操作
     * @param memento 快照
     */
    public restoreMemento(memento: Memento) {
        this.money = memento.money;
        this.fruits = memento.getFruits();
    }
    public toString(): string {
        return `[money = ${this.money}, fruits = ${JSON.stringify(this.fruits)}]`
    }
    /**
     * 获取水果，增加随机数，只有大于0.5才表示水果是好吃的
     */
    private getFruit(): string {
        let prefix = "";
        if (Math.random() >= 0.5) {
            prefix = "好吃的";
        }

        return `${prefix}${this.fruitname[Math.floor(Math.random() * this.fruitname.length)]} `
    }
}
```
#index.ts运行
```
import Gamer from "./Gamer";
import Memento from "./Memento";

const gamer: Gamer = new Gamer(100);
let memento: Memento = gamer.createMemento();
for (let i = 0; i < 100; i++) {
    console.log(`======${i}`);
    console.log(`当前状态：${gamer}`);
    gamer.bet();
    console.log(`所持金钱为${gamer.getMoney()}元`);
    console.log(`${gamer.getMoney()}元,${memento.getMoney()}`);
    if (gamer.getMoney() > memento.getMoney()) {
        console.log("所持金钱增加了许多，因此保存当前游戏状态");
        memento = gamer.createMemento();
    } else if (gamer.getMoney() < memento.getMoney() / 2) {
        console.log("所持金钱减少了许多，因此将游戏恢复至以前的状态");
        gamer.restoreMemento(memento);

    }
}
```
## 结果
```
======0
当前状态：[money = 100, fruits = []]
所持金钱增加了。
所持金钱为200元
200元,100
所持金钱增加了许多，因此保存当前游戏状态
======1
当前状态：[money = 200, fruits = []]
所持金钱减半
所持金钱为100元
100元,200
======2
当前状态：[money = 100, fruits = []]
所持金钱增加了。
所持金钱为200元
200元,200
======3
当前状态：[money = 200, fruits = []]
所持金钱减半
所持金钱为100元
100元,200
======4
当前状态：[money = 100, fruits = []]
什么都没有发生。
所持金钱为100元
100元,200
======5
当前状态：[money = 100, fruits = []]
什么都没有发生。
所持金钱为100元
100元,200
======6
当前状态：[money = 100, fruits = []]
什么都没有发生。
所持金钱为100元
100元,200
======7
当前状态：[money = 100, fruits = []]
所持金钱增加了。
所持金钱为200元
200元,200
======8
当前状态：[money = 200, fruits = []]
什么都没有发生。
所持金钱为200元
200元,200
======9
当前状态：[money = 200, fruits = []]
什么都没有发生。
所持金钱为200元
200元,200
```
##类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-965b1afc66f88f92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 结构
- Originator（生成者）
Originator角色保存自己的最新状态时生产Memento角色，当吧以前保存的Memento角色传递给Originator角色时，它会将自己恢复至生成该Memento角色时的状态。在示例中Gamer类扮演该角色
- Memento（备忘录）
Memento角色会将Originator角色的内部信息整合在一起，在Memento角色中虽然保存了Originator角色信息，但不会向外部公开这些信息
Memento角色有以下两种接口（API）
- `wide interface---宽接口`
宽接口是指所有用于获取恢复对象状态信息的方法的集合。由于宽接口会暴露所有信息，所以能使用宽接口的只有Originator角色
- `narrow interface ---窄接口`
可以通过窄接口获取的Memento的信息有限，有效放在信息泄露

示例中Memento类扮演该角色
- Caretaker（负责人）
当Caretaker角色想要保存当前Originator角色状态时，会通知Originator角色，Originator接到同事后会生成Memento角色的实例并返回给Caretaker角色。为了Originator状态恢复，Caretaker会一直保存Memento实例。
示例中index扮演该角色
