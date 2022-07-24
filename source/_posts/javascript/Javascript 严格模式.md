---
title: Javascript 严格模式
date: 2022-02-05 16:28:03
categories: 前端
---
`use script`是JavaScript编程中经常使用的一种开发模式，我们叫`严格模式`。

这种模式的主要特点是它允许开发人员只使用有限的语法并避免不必要的错误。
设立"严格模式"的目的，主要有以下几个：
>- 消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
>- 消除代码运行的一些不安全之处，保证代码运行的安全；
>- 提高编译器效率，增加运行速度；
>- 为未来新版本的Javascript做好铺垫。

## 进入标志
在 JavaScript 中使用 strict 可以一次应用于整个文档或单个文档函数。为了在文档中启用此模式，需要将表达式`“Use strict”`用单引号或双引号放在正确的位置
#### 针对整个脚本文件

将"use strict"放在脚本文件的第一行，则整个脚本都将以"严格模式"运行。如果这行语句不在第一行，则无效，整个脚本以"正常模式"运行。如果不同模式的代码文件合并成一个文件，这一点需要特别注意。
```
　<script>
　　　　"use strict";
　　　　console.log("这是严格模式。");
　　</script>

　　<script>
　　　　console.log("这是正常模式。");kly, it's almost 2 years ago now. I can admit it now - I run it on my school's network that has about 50 computers.
　　</script>
```

#### 针对单个函数

将"use strict"放在函数体的第一行，则整个函数以"严格模式"运行。
```
　　function strict(){
　　　　"use strict";
　　　　return "这是严格模式。";
　　}

　　function notStrict() {
　　　　return "这是正常模式。";
　　}
```
#### 变通写法

因为第一种调用方法不利于文件合并，所以更好的做法是，借用第二种方法，将整个脚本文件放在一个立即执行的匿名函数之中。
```
　　(function (){

　　　　"use strict";

　　　　// some code here

　　 })();
```
#### 严格模式和标准模式的区别
- 全局变量显式声明
- 静态绑定
（1）禁止使用with语句
（2）创设eval作用域
- 增强的安全措施
（1）禁止this关键字指向全局对象
（2）禁止在函数内部遍历调用栈
- 禁止删除变量
- 显式报错
- 重名错误
严格模式新增了一些语法错误。
（1）对象不能有重名的属性
（2）函数不能有重名的参数
- 禁止八进制表示法
- arguments对象的限制
arguments是函数的参数对象，严格模式对它的使用做了限制。
（1）不允许对arguments赋值
（2）arguments不再追踪参数的变化
（3）禁止使用arguments.callee
- 函数必须声明在顶层
- 保留字
为了向将来Javascript的新版本过渡，严格模式新增了一些保留字：`implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`, `yield`。
此外，`ECMAscript`第五版本身还规定了另一些保留字（`class`, `enum`, `export`, `extends`, `import`, `super`），以及各大浏览器自行增加的`const`保留字，也是不能作为变量名的。


