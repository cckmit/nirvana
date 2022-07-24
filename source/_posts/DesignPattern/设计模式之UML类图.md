---
title: 设计模式之UML类图
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
## 类图：
在类图中一共包含了以下几种模型元素，分别是：类（Class）、接口（Interface）以及类之间的关系。

#####  1、类（Class)

　　在面向对象（OO) 编程中，类是对现实世界中一组具有相同特征的物体的抽象。

![image](https://upload-images.jianshu.io/upload_images/10024246-8bbcf2cb154f0512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####  2、 接口（Interface）

　　接口是一种特殊的类，具有类的结构但不可被实例化，只可以被实现（继承）。在UML中，接口使用一个带有名称的小圆圈来进行表示。

![image](https://upload-images.jianshu.io/upload_images/10024246-de1b5d4397a635a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####  3、类图中关系（relation）

在UML类图中，常见的有以下几种关系: 
- 泛化（Generalization）
- 实现（Realization）
- 关联（Association)
- 聚合（Aggregation）
- 组合(Composition)
- 依赖(Dependency)

#####  3.1 泛化（Generalization）

【泛化关系】：是一种继承关系，表示一般与特殊的关系，它指定了子类如何特化父类的所有特征和行为。

例如：老虎是动物的一种，即有老虎的特性也有动物的共性。

【箭头指向】：带三角箭头的实线，箭头指向父类

![泛化](https://upload-images.jianshu.io/upload_images/10024246-029720a88a5da64a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#####  3.2 实现（Realization）

【实现关系】：是一种类与接口的关系，表示类是接口所有特征和行为的实现.

【箭头指向】：带三角箭头的虚线，箭头指向接口

![实现](https://upload-images.jianshu.io/upload_images/10024246-a3ae84ab7432ba6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####  3.3 关联（Association)

1、关联关系

关联关系又可进一步分为单向关联、双向关联和自关联。

（1）单向关联

![image](https://upload-images.jianshu.io/upload_images/10024246-b7da7cefc1e8a965.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以看到，在UML类图中单向关联用一个带箭头的直线表示。上图表示每个顾客都有一个地址，这通过让Customer类持有一个类型为Address的成员变量类实现。

（2）双向关联

![image](https://upload-images.jianshu.io/upload_images/10024246-8a5ab4ef3ea8fca7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图中我们很容易看出，所谓的双向关联就是双方各自持有对方类型的成员变量。在UML类图中，双向关联用一个不带箭头的直线表示。上图中在Customer类中维护一个Product[]数组，表示一个顾客购买了那些产品；在Product类中维护一个Customer类型的成员变量表示这个产品被哪个顾客所购买。

（3）自关联

![image](https://upload-images.jianshu.io/upload_images/10024246-d105b28536055929.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自关联在UML类图中用一个带有箭头且指向自身的直线表示。上图的意思就是Node类包含类型为Node的成员变量，也就是“自己包含自己”。

>**依赖和关联区别：**
*用锤子修了一下桌子，我和锤子之间就是一种依赖，我和我的同事就是一种关联。
依赖是一种弱关联，只要一个类用到另一个类，但是和另一个类的关系不是太明显的时候（可以说是“uses”了那个类），就可以把这种关系看成是依赖，依赖也可说是一种偶然的关系，而不是必然的关系。
关联是类之间的一种关系，例如老师教学生，老公和老婆这种关系是非常明显的。依赖是比较陌生，关联是我们已经认识熟悉了。*
#####  3.4 聚合（Aggregation）

【聚合关系】：是整体与部分的关系，且部分可以离开整体而单独存在。

如车和轮胎是整体和部分的关系，轮胎离开车仍然可以存在。

聚合关系是关联关系的一种，是强的关联关系；关联和聚合在语法上无法区分，必须考察具体的逻辑关系。

【代码体现】：成员变量

【箭头及指向】：带空心菱形的实心线，菱形指向整体

![image.png](https://upload-images.jianshu.io/upload_images/10024246-5e72b3273da1726a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####  3.5 组合(Composition)

【组合关系】：是整体与部分的关系，但部分不能离开整体而单独存在。

如公司和部门是整体和部分的关系，没有公司就不存在部门。

组合关系是关联关系的一种，是比聚合关系还要强的关系，

它要求普通的聚合关系中代表整体的对象负责代表部分的对象的生命周期。

【代码体现】：成员变量

【箭头及指向】：带实心菱形的实线，菱形指向整体

![image.png](https://upload-images.jianshu.io/upload_images/10024246-f091bcde6b547433.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####  3.6 依赖(Dependency)

【依赖关系】：是一种使用的关系，即一个类的实现需要另一个类的协助，

所以要尽量不使用双向的互相依赖.

【代码表现】：局部变量、方法的参数或者对静态方法的调用

【箭头及指向】：带箭头的虚线，指向被使用者

![image.png](https://upload-images.jianshu.io/upload_images/10024246-4b696f117362a3ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


各种关系的强弱顺序：

泛化 = 实现 > 组合 > 聚合 > 关联 > 依赖

下面这张UML图，比较形象地展示了各种类图关系：

![image](https://upload-images.jianshu.io/upload_images/10024246-f8c0a29ab9d7ffa4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**类图绘制的要点**

- 类的操作是针对类自身的操作，而不是它去操作人家。比如书这个类有上架下架的操作，是书自己被上架下架，不能因为上架下架是管理员的动作而把它放在管理员的操作里。
- 两个相关联的类，需要在关联的类中加上被关联类的ID，并且箭头指向被关联类。可以理解为数据表中的外键。比如借书和书，借书需要用到书的信息，因此借书类需包含书的ID，箭头指向书。
- 由于业务复杂性，一个显示中的实体可能会被分为多个类，这是很正常的，类不是越少越好。类的设计取决于怎样让后台程序的操作更加简单。比如单看逻辑，借书类可以不存在，它的信息可以放在书这个类里。然而借还书和书的上架下架完全不是一回事，借书类对借书的操作更加方便，不需要去重复改动书这个类中的内容。此外，如果书和借书是1对多的关系，那就必须分为两个类。
- 类图中的规范问题，比如不同关系需要不同的箭头，可见性符号等。
