---
title: require和import
date: 2021-03-11 21:12:40
categories: 前端
---------
ES6标准发布后，module成为标准，标准的使用是以export指令导出接口，以import引入模块，但是在我们一贯的node模块中，我们采用的是CommonJS规范，使用require引入模块，使用module.exports导出接口。

#####[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)
CommonJS定义的模块分为:{模块引用(require)} {模块定义(exports)} {模块标识(module)}
require()用来引入外部模块；exports对象用于导出当前模块的方法或变量，唯一的导出口；module对象就代表模块本身。
require统治了ES6之前的所有模块化编程，即使现在，在ES6 module被完全实现之前，还是这样。

```
// a.js

// -------- node -----------
module.exports = {
  a : function() {},
  b : 'xxx'
};
// b.js

// ------------ node ---------
var m = require('./a');
m.a();
```
**require()是同步方法**
ES6发布的module并没有直接采用CommonJS，甚至连require都没有采用，也就是说require仍然只是node的一个私有的全局方法，module.exports也只是node私有的一个全局变量属性

***注意：module.exports方法不会多次生效，只执行最后一行语句的导出方法，这个与下面要介绍的export是有区别的，后面会介绍这个***

###ES6 模块
ES6 模块不是对象，而是通过export命令显式指定输出的代码，再通过import命令输入。
模块功能主要由两个命令构成：export和import。export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。


```
// ES6模块
import { stat, exists, readFile } from 'fs';
```
上面代码的实质是从fs模块加载 3 个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。但是这样会造成import时无法动态加载

```
//react native根据平台动态加载js，无法使用import方式加载
var NativeAPI = require('./Native_API/NativeAPI.' + Platform.OS);
```
###[export](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export)导出模块

```
// module "my-module.js"
function cube(x) {
  return x * x * x;
}
const foo = Math.PI + Math.SQRT2;
export { cube, foo };
```
**注意，export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。但是module.exports是可以的**

```
// 错误演示
// 报错
export 1; // 绝对不可以

// 报错
var a = 100;
export a;

//module.exports
var a = 1;
module.exports = a
```
export在导出接口的时候，必须与模块内部的变量具有一一对应的关系。直接导出1没有任何意义，也不可能在import的时候有一个变量与之对应。 export a 虽然看上去成立，但是 a 的值是一个数字，根本无法完成解构，因此必须写成 export {a} 的形式。即使a被赋值为一个function，也是不允许的。

```
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```

***注意：export可以在代码中导出多个***

```
var m = 1;
export {m};//导出m

var b = 2;
export {b}//导出b
```

###[import](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import)导入模块

import的语法跟require不同，而且import必须放在文件的最开始，且前面不允许有其他逻辑代码。

```
// import "user.js"
import { cube, foo } from 'my-module';
console.log(cube(3)); // 27
console.log(foo);    // 4.555806215962888
```

import后面跟上花括号的形式是最基本的用法，花括号里面的变量与export后面的变量一一对应。这里，你必须了解 [对象的解构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 的知识，也可参考[阮一峰的结构赋值](http://es6.ruanyifeng.com/#docs/destructuring)

###as关键字
简单的说就是取一个别名。export中可以用，import中其实可以用：
```
// a.js
var a = function() {};
export {a as fun};

// b.js
import {fun as a} from './a';
a();
```
export的时候，对外提供的接口是fun，它是a.js内部a这个函数的别名，但是在模块外面，认不到a，只能认到fun。
import中的as就很简单，就是你在使用模块里面的方法的时候，给这个方法取一个别名，好在当前的文件里面使用。**两个不同文件可能都export同一个fun的对象**
###export default 命令
export default命令，为模块指定默认输出。

```
// 第一组
export default function crc32() { // 输出
  // ...
}
import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};
import {crc32} from 'crc32'; // 输入
```
default其实是as的语法糖，这个语法糖的好处就是import的时候，可以省去花括号{}。
###*符号
*代表所有

```
 import * as _ from 'xxx.js';//表示把xxx模块中的所有接口挂载到 _ 这个对象上
```
通过*号直接继承某一个模块的接口
```
export * from '_';

// 等效于：
import * as all from '_';
export all;
```
尽量少用，它使用的所有的export的接口，但是你当前模块其实并不一定需要用到所有的接口，还是用{}按照需要去引用

###Require Or Import
require理论上可以运用在代码的任何地方，甚至不需要赋值给某个变量之后再使用；但是import则不同，它是编译时的（require是运行时的），它必须放在文件开头，而且使用格式也是确定的，不容置疑。它不会将整个模块运行后赋值给某个变量，而是只选择import的接口进行编译，这样在性能上比require好很多。

按照规范***module.exports =>require，export=>import***，但是现在开发为了兼容低版本的JS引擎会对import进行降级处理，会把import转换成require；这样就造成了各种方法可以混合使用，如

```
//import.js:导出模块a和d
export class a {//分开导出
    constructor(arg=2) {
        this.t = 1;
        this.t2 = arg;
    }
    b(){
        console.log('b');
        console.log(this.t2);
    }
    c(){
        console.log('c')
        console.log(this.t);
    }
}
var d = function(){
};
export {d};

//require.js:导出模块a,d,e
class a {//分开导出
    constructor(arg=2) {
        this.t = 1;
        this.t2 = arg;
    }
    b(){
        console.log('b');
        console.log(this.t2);
    }
    c(){
        console.log('c')
        console.log(this.t);
    }
}
var d = function(){
    console.log(d)
};
var e = 1
module.exports = {a,d,e};

//index.js
var require_a = require('./require')
var import_a = require('./import')
import {a as test} from './import'
import a from './require'
import * as all_require from './require'
import * as all_import from './import'

console.log(require_a);//{a: ƒ, d: ƒ, e: 1}
console.log(import_a);//{a: ƒ, d: ƒ, __esModule: true}
console.log(test);//ƒ a()
console.log(a);//{a: ƒ, d: ƒ, e: 1}
console.log(all_require);//{a: ƒ, d: ƒ, e: 1, default: {…}}
console.log(all_import);//{a: ƒ, d: ƒ, __esModule: true}

//a1,a2数据不影响
//constructor可传参数，但参数名不能与变量名相同
var a1 = new test();
var a2 = new test(4);

console.log(a1.t,a1.t2);//1,2
a1.b();//b,2
a1.t=3;
a1.c();//c,3

console.log(a2.t,a2.t2);//1,4
a2.b();//b,4
a2.t=5;
a2.c();//c,5

```
可以看到在混用时require和import在编码是是都可以使用的，需要注意的是require是把所有模块加载进去，与import * 基本相同，但是import *会增加default属性。import需要指定模块名，如果不存在会返回undefined。

***require 取到整个对象***

***import a 取到default的对象，如果无default对象，在编译时会报错***

***import {a} 取到 a对象***

***import \* as XX 取到所有对象，并放到XX里面***

```
//a.js
export default class xxxx {}

var xxx = require('./a.js');//{default:ƒ xxx()}
import xxx from './a.js';//ƒ xxx()
```
当export有default时,在使用require时，需要从default中才能取到要使用的对象，而import可以直接拿到对应的属性

```
//a.js
module.exports = class a {}

var a = require('./a.js');//ƒ xxx()
import a from './a.js';//ƒ xxx()
```
如果module.exports导出的是单一的对象时，使用require和import取到的对象是同样的，都是可以直接使用，和default的效果基本一致。

***注意：webpack3在module.exports编译时，在模块前自增加了default属性***
