---
title: 利用 JSON.stringify 深拷贝的弊端
date: 2021-04-21 14:45:27
categories: 前端
---
深拷贝不仅将原对象的各个属性逐个复制出去，而且将原对象各个属性所包含的对象也依次采用深拷贝的方法递归复制到新对象上。
```
function deepClone(obj){
    let obj_ = JSON.stringify(obj),
    loneObj = JSON.parse(obj_);
    return loneObj
}
let a=[1,2,3,4,[2,3,4]],
b = deepClone(a);
console.log(a,b);
```
结果：
![](https://upload-images.jianshu.io/upload_images/10024246-e4e276aba8a446aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 弊端：
##### 1.如果 obj 里面有时间对象，则 JSON.stringify 后再 JSON.parse 的结果，时间将转为字符串的形式，而不是对象的形式
```
let obj = {
    a:123,
    b:[new Date(),new Date()]
}
let newObj = JSON.parse(JSON.stringify(obj));
console.log(obj,newObj);
```
例如：
![](https://upload-images.jianshu.io/upload_images/10024246-f7c03875bd5412ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 2.如果 obj 里有 RegExp (正则表达式的缩写)、Error 对象，则序列化的结果将只得到空对象；
```
let obj = {
    a:123,
    b:/aabb[a-z]\d/,
    c:new TypeError('typeError')
}
let newObj = JSON.parse(JSON.stringify(obj));
console.log(obj,newObj);
```
![](https://upload-images.jianshu.io/upload_images/10024246-0e273cafa2f303ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-814ec09b0a942374.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




##### 3、如果 obj 里有函数（function），undefined，Symbol 则序列化的结果会把 function 或 undefined 丢失；
```
let obj = {
    a:123,
    fn:function(){let a = 123;console.log(a)},
    c:undefined,
    d:Symbol('foo')
}
let newObj = JSON.parse(JSON.stringify(obj));
console.log(obj,newObj);
```
![](https://upload-images.jianshu.io/upload_images/10024246-25b032809dfcf831.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 4、如果obj里有 NaN、Infinity 和 -Infinity，则序列化的结果会变成 null
```
let obj = {
    a:123,
    b:NaN,
    c:Infinity,
    d:-Infinity
}
let newObj = JSON.parse(JSON.stringify(obj));
console.log(obj,newObj);
```
![](https://upload-images.jianshu.io/upload_images/10024246-7b63f03dccb24601.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 5、JSON.stringify() 只能序列化对象的可枚举的自有属性，例如如果 obj 中的对象是有构造函数生成的， 则使用 JSON.parse(JSON.stringify(obj)) 深拷贝后，会丢弃对象的 constructor；
```
class Student{
    constructor(){
        this.a = 1;
        this.b = 2;
    }
}
let obj = {
    a:123,
    b:new Student()
}
let newObj = JSON.parse(JSON.stringify(obj));
console.log(obj,newObj);
 ```
![](https://upload-images.jianshu.io/upload_images/10024246-71619104d9168a18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




手动设置某属性不可枚举：
```
let obj = {
    a:123,
    b:234
}
Object.defineProperty(obj,'c',{
    value:22,
    enumerable:false,
})
let newObj = JSON.parse(JSON.stringify(obj));
console.log(obj,newObj);
 ```
![](https://upload-images.jianshu.io/upload_images/10024246-739d98fb75d5d364.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 6、如果对象中存在循环引用的情况也无法正确实现深拷贝；
总结：

- Date 对象变为字符串
- RegExp、Error 对象变为空对象 {}
- 函数、undefined、Symbol 属性丢失
- NaN、Infinity、-Infinity 变为 null
- enumerable 为 false 的属性丢失
- 循环引用的对象不能正确拷贝
用法简单，然而使用这种方法会有一些隐藏的坑：因为在序列化 JavaScript 对象时，所有函数和原型成员会被有意忽略。

通俗点说，JSON.parse(JSON.stringfy(X))，其中 X 只能是 Number, String, Boolean, Array,扁平对象，即那些能够被 JSON 直接表示的数据结构。

##  JSON.stringify() 常用方式
1.判断数组是否包含某对象，或者判断对象是否相等
是否相等：
```
let obj = {
    a:123,
    b:456,
}
let obj2 = {
    a:123,
    b:456,
}
console.log(JSON.stringify(obj) === JSON.stringify(obj2));//true
```
2.是否包含：
```
let arr = [
    {a:1,b:2},
    {a:2,b:3},
    {a:3,b:4}
]
let obj = {a:2,b:3}
console.log(JSON.stringify(arr).includes(JSON.stringify(obj)));//true
```
3.让 localStorage / sessionStorage 可以存储对象。
localStorage/sessionStorage 默认只能存储字符串，而实际开发中，我们往往需要存储的数据多为对象类型，那么这里我们就可以在存储时利用 Json.stringify() 将对象转为字符串,取出时利用 JSON.parse() 将字符串转为对象即可。
```
sessionStorage.setItem('user',JSON.stringify({name:'aaa',age:16}));
console.log(JSON.parse(sessionStorage.getItem('user')));//{name: "aaa", age: 16}
```
4.JSON.stringify()与toString()这两者虽然都可以将目标值转为字符串，但本质上还是有区别的
```
let arr = [12,12,12];
console.log(JSON.stringify(arr),arr.toString());
//[12,12,12] 12,12,12
 ````