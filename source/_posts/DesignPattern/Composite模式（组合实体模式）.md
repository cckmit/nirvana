---
title: Composite模式（组合实体模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
## 组合实体模式
组合实体模式（Composite Entity Pattern）能够是容器和内容具有一致性，创造出递归结构的模式。
常见使用场景：如树形菜单、文件夹菜单、部门组织架构图等。
## 示例：
### Component:抽象类
```
export default abstract class Component {
    public abstract Opertion(): void; //操作
    public abstract Add(Child: Component): void;//添加子节点
    public abstract Remove(index: number): void;//删除节点
    public abstract getChild(index: number): Array<Component>;//获取子节点
}
```
### Leaf:叶子类
```
import Component from "./Component";


export default class Leaf extends Component {

    private leafName: string;

    constructor(leafName: string) {
        super();
        this.leafName = leafName;
    }

    public Opertion(): void {
        console.log(this.leafName + "正在迎风摇摆");
    }

    public Add(Child: Component): void {

    }

    public Remove(index: number): void {

    }

    public getChild(index: number): Array<Component> {
        return null;
    }
}
```
### Trunk:树干类
```
import Component from "./Component";

export default class Trunk extends Component {

    private TrunkName: string;

    private components: Array<Component> = []; //多个子节点

    constructor(TrunkName: string) {
        super();
        this.TrunkName = TrunkName;
    }

    public Opertion(): void {
        console.log(this.TrunkName + "树干正在抵御秋风");
        this.components.forEach((item, index) => {
            item.Opertion();
        })
    }

    public Add(Child: Component): void {
        this.components.push(Child);
    }

    public Remove(index: number): void {
        this.components.splice(index, 1);
    }

    public getChild(index: number): Array<Component> {
        return this.components;
    }
}
```
### index.ts
```
import Trunk from "./Trunk";
import Leaf from "./Leaf";

const root: Trunk = new Trunk("树根");

const trunk: Trunk = new Trunk("主干");

const t1: Trunk = new Trunk("主分支");
const t2: Trunk = new Trunk("第一分支");
const t3: Trunk = new Trunk("第二分支");

const l1: Leaf = new Leaf("分叶1");
const l2: Leaf = new Leaf("分叶2");
const l3: Leaf = new Leaf("分叶3");
const l4: Leaf = new Leaf("分叶4");
const l5: Leaf = new Leaf("分叶5");

root.Add(trunk);
trunk.Add(t1);

t1.Add(t2);
t1.Add(t3);

t2.Add(l1);
t2.Add(l3);
t2.Add(l5);

t3.Add(l2);
t3.Add(l4);

root.Opertion();
```
### result
```
树根树干正在抵御秋风
主干树干正在抵御秋风
主分支树干正在抵御秋风
第一分支树干正在抵御秋风
分叶1正在迎风摇摆
分叶3正在迎风摇摆
分叶5正在迎风摇摆
第二分支树干正在抵御秋风
分叶2正在迎风摇摆
分叶4正在迎风摇摆
```
### 类图
![类图](https://upload-images.jianshu.io/upload_images/10024246-d9e5cb13d2a82048.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### composite模式
composite模式主要有一下几类角色
- leaf 树叶
表示内容的角色，该角色中不能放入其他对象，对应我们实例程序中的leaf
- Composite 复合物
表示容器的角色，可以放入小容器和内容，也就是leaf和composite，此实例中，由Trunk类代表composite
- component
是leaf和composite角色具有一致性的角色，一般作为leaf和composite的父类，定义一些共有的行为和属性，此例中的component类代表
