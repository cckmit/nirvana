---
title: Strategy-Pattern模式（策略模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**策略模式（Strategy Pattern）**:一个类的行为或其算法可以在运行时更改。这种类型的设计模式属于行为型模式。
## 介绍
- **意图：**定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。
- **主要解决：**在有多种算法相似的情况下，使用 if...else 所带来的复杂和难以维护。
- **何时使用：**一个系统有许多许多类，而区分它们的只是他们直接的行为。
- **如何解决：**将这些算法封装成一个一个的类，任意地替换。
- **关键代码：**实现同一个接口。
- **优点：**
 1、算法可以自由切换。 
2、避免使用多重条件判断。 
3、扩展性良好。
- **缺点：**
 1、策略类会增多。
 2、所有策略类都需要对外暴露。
>注意事项：如果一个系统的策略多于四个，就需要考虑使用混合模式，解决策略类膨胀的问题。

## 示例
##### Hand.ts 表示猜拳游戏中的手势类
```
export default class Hand {
    public HANDVALUE_GUU = 0;//表示石头的值
    public HANDVALUE_CHO = 1;//表示剪刀的值
    public HANDVALUE_PAA = 2;//表示布的值

    private name: Array<string> = ["石头", "剪刀", "布"];

    private handValue: number;
    constructor(handValue: number) {
        this.handValue = handValue;
    }

    public getHand(handValue: number): Hand {
        return new Hand(handValue);
    }

    public isStrongerThan(h: Hand): boolean {
        return this.fight(h) == 1;
    }

    public isWeakerThan(h: Hand): boolean {
        return this.fight(h) == -1;
    }

    private fight(h: Hand): number {
        if (this.handValue == h.handValue) {
            return 0;
        } else if ((this.handValue + 1) % 3 > h.handValue) {
            return 1;
        } else {
            return -1;
        }
    }
    public toString(): string {
        return this.name[this.handValue];
    }
}
```
##### Strategy.ts 表示猜拳游戏中的策略的类
```
import Hand from "./Hand";

export interface Strategy {
    nextHand(): Hand;
    study(win: boolean): void;
}
```
##### WinningStrategy.ts 表示如果这局获胜，那么下一局同样手势的类
```
import { Strategy } from "./type";
import Hand from "./Hand";

export default class WinningStrategy implements Strategy {
    private win: boolean = false;
    private prevHand: Hand;
    constructor() {
    }
    public nextHand(): Hand {
        if (!this.win) {
            const random = Math.floor(Math.random() * 3);
            const hand = new Hand(random);
            this.prevHand = hand.getHand(random);
        }
        return this.prevHand;
    }
    public study(win: boolean) {
        this.win = win;
    }
}
```
##### RandomStragegy.ts 表示每次都随机手势
```
import { Strategy } from "./type";
import Hand from "./Hand";

export default class RandomStragegy implements Strategy {
    constructor() {
    }
    public nextHand(): Hand {
        const random = Math.floor(Math.random() * 3);
        const hand = new Hand(random);
        return hand.getHand(random);
    }
    public study(win: boolean) {
    }
}
```
##### Player.ts 猜拳游戏的选手类
```
import { Strategy } from "./type";
import Hand from "./Hand";

export default class Play {
    private name: string;
    private winCount: number = 0;
    private loseCount: number = 0;
    private gameCount: number = 0;
    private stragegy: Strategy
    constructor(name: string, stragegy: Strategy) {
        this.name = name;
        this.stragegy = stragegy;
    }

    public nextHand(): Hand {
        return this.stragegy.nextHand()
    }

    public win(): void {
        this.stragegy.study(true);
        this.winCount++;
        this.gameCount++;
    }
    public lose(): void {
        this.stragegy.study(false);
        this.loseCount++;
        this.gameCount++;
    }
    public even() {
        this.gameCount++;
    }
    public toString(): string {
        return `[${this.name}:${this.gameCount} games,${this.winCount} win,${this.loseCount} lose]`
    }
}
```
##### index.ts 
```
import Play from "./Play";
import WinningStrategy from "./WinningStrategy";
import RandomStragegy from "./RandomStragegy";

const play1: Play = new Play("Trao", new WinningStrategy());//坚持上一次赢
const play2: Play = new Play("Haha", new RandomStragegy());//随机
for (let i = 0; i < 20; i++) {
    const nextHand1 = play1.nextHand();
    const nextHand2 = play2.nextHand();
    if (nextHand1.isStrongerThan(nextHand2)) {
        console.log(`Winner ${play1.toString()} ${nextHand1.toString()}`);
        play1.win();
        play2.lose();
    } else if (nextHand2.isStrongerThan(nextHand1)) {
        console.log(`Winner ${play2.toString()}  ${nextHand2.toString()}`);
        play2.win();
        play1.lose();
    } else {
        console.log(`Even... ${nextHand1.toString()}`);
        play2.even();
        play1.even();
    }
}
```
##### result 
```
Even... 布
Even... 布
Even... 布
Even... 剪刀
Winner [Haha:5 games,1 win,0 lose]  剪刀
Winner [Trao:6 games,1 win,1 lose] 剪刀
Even... 剪刀
Even... 剪刀
Even... 剪刀
Winner [Trao:10 games,2 win,1 lose] 剪刀
Even... 剪刀
Winner [Trao:12 games,3 win,1 lose] 剪刀
Even... 剪刀
Winner [Trao:14 games,4 win,1 lose] 剪刀
Winner [Trao:15 games,5 win,1 lose] 剪刀
Even... 剪刀
Even... 剪刀
Winner [Trao:18 games,6 win,1 lose] 剪刀
Winner [Trao:19 games,7 win,1 lose] 剪刀
Even... 剪刀
————————————Result————————————————
Result [Trao:20 games,7 win,1 lose]
Result [Haha:20 games,1 win,7 lose]
```
## 类图
![](https://upload-images.jianshu.io/upload_images/10024246-78dcce422e6de9d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 角色
- Strategy（策略）
Strategy角色决定实现策略所必须的接口
- ConcreteStrategy（具体的策略）
ConcreteStrategy角色负责实现Strategy角色的接口，即负责实现具体的策略（方法、算法）。
- Context（上下文）
负责使用Strategy角色。Context角色保存ConcreteStrategy角色的实例，彬使用ConcreteStrategy角色去实现需求
