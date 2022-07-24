---
title: Prototype-Pattern(原型模式）
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
**原型模式（Prototype Pattern）**：是指原型实例指向创建对象的种类，并通过拷贝这些原型创建新的对象，是一种用来创建对象的模式，也就是创建一个对象作为另一个对象的prototype属性。这种类型的设计模式属于创建型模式
在js中原型设计模式的使用要比java、C中要简单的多。
## 1、Object.create()
  >1、定义: 创建一个可指定原型对象的并可以包含可选自定义属性的对象；
2、 Object.create(proto [, properties]);  可选，用于配置新对象的属性;
```
方法说明
1. proto: 要创建新对象的 原型，必须，可为 null; 这个 proto 要是已经创建的[new过]，或 对象.prototype 才有价值;

2. properties: 可选，结构为:

{

     propField: {

           value: 'val'|{}|function(){},

           writable: true|false,

           enumerable: true|false,

           configurable: true|false,

           get:function(){return 10},

           set:function(value){}

     }

}

自定的属性有以下的四种本置属性：

value: 自定义属性值;

writable: 该项值是否可编辑，默认为 false, 当为 true 时，obj.prodField 可赋值；否则只读;

enumerable: 可枚举; 

confirurable: 可配置;
还可以包含 set, get 访问器方法；
其中，[set, get] 与 value 和 writable 不能同时出现；

```
##### 示例
```
* 原型模式使用
 * Object.create
 * **/
const person = {
  isHuman: false,
  printIntroduction: function() {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = 'Matthew'; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction();
// expected output: "My name is Matthew. Am I human? true"
```
## 2、prototype 来创建对象变量
```
/***
 * 使用prototype来创建实例对象
 * @param name
 * @returns {createPerson.Person}
 */
function createPerson(name){
  function  Person(){
};
 
  Person.prototype = person;
  let newPerson = new Person();
  newPerson.name = name;
  return newPerson;
}


let me = createPerson("Matthew");
me.isHuman = true;

me.printIntroduction();
// expected output: "My name is Matthew. Am I human? true"
```
## `java版类图`
![](https://upload-images.jianshu.io/upload_images/10024246-b2c242cddd8b223f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
