---
title: Javascript函数式编程要掌握的知识点讲解
date: 2021-03-11 21:12:40
categories: 前端
---

**一：理解call和apply 及arguments.callee**

ECMAScript3给Function的原型定义了两个方法，他们是`Function.prototype.call` 和 `Function.prototype.apply`. 其实他们的作用是一样的，只是传递的参数不一样而已；

**1\. apply;** 接受2个参数，第一个参数指定了函数体内this对象的指向，第二个参数为一个类似数组的集合，比如如下代码：

```
var yunxi = function(a,b){

   console.log([a,b]); // [1,2]

   console.log(this === window); // true

};

yunxi.apply(null,[1,2]);
```

如上代码，我们第一个参数传入null，函数体内默认会指向与宿主对象，即window对象；因此我们可以在yunxi函数内打印下值为true即可看到：

下面我们来看看使用call方法的实例如下：

```
var yunxi = function(a,b){

   console.log([a,b]); // [1,2]

   console.log(this === window); // true

};

yunxi.call(null,1,2);
```

可以看到 call方法的第二个参数是以逗号隔开的参数；

**那么call和apply用在什么地方呢？**

1.call和apply 最常见的用途是改变函数体内的this指向，如下代码:

```
var longen = {
    name:'yunxi'
};
var longen2 = {
    name: '我叫涂根华'
};
var name = "我是来测试的";
var getName = function(){
    return this.name;
};
console.log(getName()); // 打印 "我是来测试的";
console.log(getName.call(longen)); // 打印 yunxi
console.log(getName.call(longen2)); // 打印 "我叫涂根华"
```

第一次调用 `getName()`方法，因为它是普通函数调用，所以它的this指向与window，因此打印出全局对象的name的值；

第二次调用`getName.call(longen);`执行这句代码后，getName这个方法的内部指针this指向于longen这个对象了，因此打印`this.name`实际上是longen.name，因此返回的是`name=”yunxi”;`

但是this指针也有列外的情况，比如一个点击元素，当我们点击一个元素的时候，this指针就指向与那个点击元素，但是当我们在内部再包含一个函数后，在函数内再继续调用this的话，那么现在的this指针就指向了window了；比如如下代码：

```
document.getElementById("longen").onclick = function(){
    console.log(this); // this 就指向于div元素对象了
    var func = function(){
        console.log(this); // 打印出window对象
    }
    func();
}
```

如上代码。可以看到外部this指向与被点击的那个元素，内部普通函数调用，this指针都是指向于window对象。但是我们可以使用call或者apply方法来改变this的指针的；如下代码：

```
document.getElementById("longen").onclick = function(){
    console.log(this); // this 就指向于div元素对象了
    var func = function(){
        console.log(this); // 就指向于div元素对象了
    }
    func.call(this);
}
```

如上代码我们使用call方法调用func函数，使this指向与func这个对象了，当然上面的方法我们还可以不使用call或者apply方法来改变this的指针，我们可以在外部先使用一个变量来保存this的指针，在内部调用的时候我们可以使用哪个变量即可，如下代码演示：

```
document.getElementById("longen").onclick = function(){
    console.log(this); // this 就指向于div元素对象了
    var self = this;
    var func = function(){
        console.log(self); // 就指向于div元素对象了
    }
    func();
}  
```

**arguments.callee的理解**

callee是arguments的一个属性，它可以被用作当前函数或函数体执行的环境中，或者说调用一个匿名函数；返回的是当前正在被执行的Function对象；简单的来说就是当前执行环境的函数被调用时候，arguments.callee对象会指向与自身，就是当前的那个函数的引用；

如下代码：

```
var count = 1;
var test = function() {
    console.log(count + " -- " + (test.length == arguments.callee.length) );
    // 打印出 1 -- true 2 -- true  3 -- true 
    if (count++ < 3) {
        // 调用test()函数自身
        arguments.callee();
    }
};
test();
```

`arguments.callee()`的含义是调用当前正在执行的函数自身，比如上面的test的匿名函数；

**Function.prototype.bind介绍**

目前很多高级浏览器都支持`Function.prototype.bind`方法，该方法用来指定函数内部的this指向。为了支持各个浏览器，我们也可以自己来简单的模拟一个~

如下代码：

```
Function.prototype.bind = function(context) {
    var self = this;
    return function(){
        return self.apply(context,arguments);
    }
}
var yunxi = {
    name: 'yunxi'
};

var func = function(){
    console.log(this.name); // yunxi
}.bind(yunxi);

func();
```

如上代码所示：func这个函数使用调用bind这个方法，并且把对象yunxi作为参数传进去，然后bind函数使用return返回一个函数，当我们调用`func()`执行这个方法的时候，其实我们就是在调用bind方法内的return返回的那个函数，在返回的那个函数内context的上下文其实就是我们以参数yunxi对象传进去的，因此this指针指向与yunxi这个对象了~ 所以打印出`this.name` 就是yunxi那个对象的name了;

除了上面我们看到的介绍apply或者call方法可以改变this指针外，我们还可以使用call或者apply来继承对象的方法；实质也就是改变this的指针了；

比如有如下代码：

```
var Yunxi = function(name){
    this.name = name;
};

var Longen = function(){
    Yunxi.apply(this,arguments);
};

Longen.prototype.getName = function(){
    return this.name;
};

var longen = new Longen("tugenhua");
console.log(longen.getName());  // 打印出tugenhua
```

如上代码：我先实例化Longen这个对象，把参数传进去，之后使用`Yunxi.apply(this,arguments)`这句代码来改变Longen这个对象的this的指针，使他指向了Yunxi这个对象，因此Yunxi这个对象保存了longen这个实例化对象的参数tugenhua，因此当我们调用`longen.getName`这个方法的时候，我们返回`this.name`，即我们可以认为返回的是`Yunxi.name` 因此返回的是 tugenhua，我们只是借用了下Yunxi这个对象内的`this.name`来保存Longen传进去的参数而已；

**二：闭包的理解**

**闭包的结构有如下2个特性**

1.封闭性：外界无法访问闭包内部的数据，如果在闭包内声明变量，外界是无法访问的，除非闭包主动向外界提供访问接口；

2.持久性：一般的函数，调用完毕之后，系统自动注销函数，而对于闭包来说，在外部函数被调用之后，闭包结构依然保存在

系统中，闭包中的数据依然存在，从而实现对数据的持久使用。

缺点：

使用闭包会占有内存资源，过多的使用闭包会导致内存溢出等.

如下代码：

```
function a(x) {
    var a = x;
    var b = function(){
        return a;
    }
    return b;
}
var b = a(1);
console.log(b()); // 1
```

首先在a函数内定义了2个变量，1个是存储参数，另外一个是闭包结构，在闭包结构中保存着b函数内的a变量，默认情况下，当a函数调用完之后a变量会自动销毁的，但是由于闭包的影响，闭包中使用了外界的变量，因此a变量会一直保存在内存当中，因此变量a参数没有随着a函数销毁而被释放，因此引申出闭包的缺点是：过多的使用闭包会占有内存资源，或内存溢出等肯能性；

```
// 经典的闭包实列如下：
function f(x){              //外部函数
    var a = x;              // 外部函数的局部变量，并传递参数
    var b = function(){     // 内部函数 
        return a;           // 访问外部函数中的局部变量
    };
    a++;                    // 访问后，动态更新外部函数的变量
    return b;               // 返回内部函数
}
var c = f(5);               // 调用外部函数并且赋值
console.log(c());           // 调用内部函数，返回外部函数更新后的值为6
```

**下面我们来看看如下使用闭包的列子**

在如下代码中有2个函数，f函数的功能是：把数组类型的参数中每个元素的值分别封装在闭包结构中，然后把闭包存储在一个数组中，并返回这个数组，但是在函数e中调用函数f并向其传递一个数组`["a","b","c"]`,然后遍历返回函数f返回数组，我们运行打印后发现都是c undefined，那是因为在执行f函数中的循环时候，把值虽然保存在temp中，但是每次循环后temp值在不断的变化，当for循环结束后，此时temp值为c，同时i变为3，因此当调用的时候 打印出来的是temp为3，arrs[3]变为undefined；因此打印出 c undefined

解决闭包的缺陷我们可以再在外面包一层函数，每次循环的时候，把temp参数和i参数传递进去 如代码二

```
// 代码一
function f(x) {
    var arrs = [];
    for(var i = 0; i < x.length; i++) {
        var temp = x[i];
        arrs.push(function(){
            console.log(temp + ' ' +x[i]); // c undefined
        });
    }
    return arrs;
}
function e(){
    var ar = f(["a","b","c"]);
    for(var i = 0,ilen = ar.length; i < ilen; i++) {
        ar[i]();
    }
}
e(); 
// 代码二：
function f2(x) {
    var arrs = [];
    for(var i = 0; i < x.length; i++) {
        var temp = x[i];
        (function(temp,i){
            arrs.push(function(){
                console.log(temp + ' ' +x[i]); // c undefined
            });
        })(temp,i);
    }
    return arrs;
}
function e2(){
    var ar = f2(["a","b","c"]);
    for(var i = 0,ilen = ar.length; i < ilen; i++) {
        ar[i]();
    }
}
e2(); 
```

**三：javascript中的this详解**

this的指向常见的有如下几点需要常用到：

1.  全局对象的this是指向与window；
2.  作为普通函数调用。
3.  作为对象方法调用。
4.  构造器调用。
5.  `Function.prototype.call` 或 `Function.prototype.apply`调用。

下面我们分别来介绍一下

**1.全局对象的this；**

```
console.log(this); // this指向于window
setTimeout() 和 setInterval()函数内部的this指针是指向于window的，如下代码：
 function test(){
    console.log(11);
 }
 setTimeout(function(){
    console.log(this === window); // true
    this.test(); // 11 
 });
```

**2.作为普通函数调用；**

如下代码：

```
var name = "longen";
function test(){
    return this.name;
}
console.log(test()); // longen
```

当作为普通函数调用时候，this总是指向了全局对象，在浏览器当中，全局对象一般指的是window；

**3.作为对象的方法调用。**

如下代码：

```
var obj = {
    "name": "我的花名改为云溪了，就是为了好玩",
    getName: function(){
        console.log(this); // 在这里this指向于obj对象了
        console.log(this.name); // 打印 我的花名改为云溪了，就是为了好玩
    }
};
obj.getName(); // 对象方法调用
```

但是呢，我们不能像如下一样调用对象了，如下调用对象的话，this还是执行了window，如下代码：

```
var name = "全局对象名字";
var obj = {
    "name": "我的花名改为云溪了，就是为了好玩",
    getName: function(){
        console.log(this);  // window
        console.log(this.name); // 全局对象名字
    }
};
var yunxi = obj.getName; 
yunxi();
```

运行yunxi()函数，还是会像调用普通函数一样，this指向了window的；

**4.构造器调用。**
Javascript中不像Java一样，有类的概念，而JS中只能通过构造器创建对象，通过new 对象，当new运算符调用函数时候，该函数会返回一个对象，一般情况下，构造器里面的this就是指向返回的这个对象；

如下代码：

```
var Obj = function(){
    this.name = "yunxi";
};
var test = new Obj();
console.log(test.name); // yunxi
```

注意：构造器函数第一个字母需要大写，这是为了区分普通函数还是构造器函数而言；

如上代码：通过调用`new Obj()`方法 返回值保存到test变量中，那么test就是那个对象了，所以内部的this就指向与test对象了，因此`test.name`就引用到了内部的`this.name` 即输出 “yunxi”字符串；

但是也有例外的情况，比如构造器显示地返回了一个对象的话，那么这次继续调用的话，那么会最终会返回这个对象，比如如下代码：

```
var obj = function(){
    this.name = "yunxi";
    return {
        "age": "27"
    }
};
var test = new obj();
console.log(test.name); // undefined
```

那么继续调用的话，会返回unedfined，因为返回的是那个对象，对象里面没有name这个属性，因此值为undefined；

**四：理解函数引用和函数调用的区别**

看下面的代码分析:

```
// 函数引用 代码一
function f(){
    var x = 5;
    return x;
}
var a = f;
var b = f;

console.log(a===b); // true
// 函数调用 代码二
function f2() {
    var x = 5;
    return x;
}
var a2 = f2();
var b2 = f2();
console.log(a2 === b2);

// 函数调用 代码三
function f3(){
    var x = 5;
    return function(){
        return x;
    }
}
var a3 = f3();
var b3 = f3();
console.log(a3 === b3); // false
```

如上的代码：代码一和代码二分部是函数引用和函数调用的列子，返回都为true，代码三也是函数调用的列子，返回且为false

我们现在来理解下函数引用和函数调用的本质区别：当引用函数时候，多个变量内存存储的是函数的相同的入口指针，因此对于同一个函数来讲，无论多少个变量引用，他们都是相等的，因为对于引用类型(对象，数组，函数等)都是比较的是内存地址，如果他们内存地址一样的话，说明是相同的；但是对于函数调用来讲，比如代码三;每次调用的时候，都被分配一个新的内存地址，所以他们的内存地址不相同，因此他们会返回false，但是对于代码二来讲，我们看到他们没有返回函数，只是返回数值，他们比较的不是内存地址，而是比较值，所以他们的值相等，因此他们也返回true，我们也可以看看如下实列化一个对象的列子，他们也被分配到不同的内存地址，因此他们也是返回false的；如下代码测试：

```
function F(){
    this.x = 5;
}
var a = new F();
var b = new F();
console.log(a === b); // false
```

**五：理解js中的链式调用**

我们使用jquery的时候，jquery的简单的语法及可实现链式调用方法，现在我们自己也封装一个链式调用的方法，来理解下 jquery中如何封装链式调用 无非就是每次调用一个方法的时候 给这个方法返回this即可，this指向该对象自身，我们看看代码：

```
// 定义一个简单的对象，每次调用对象的方法的时候，该方法都返回该对象自身
var obj = {
    a: function(){
        console.log("输出a");
        return this;
    },
    b:function(){
        console.log("输出b");
        return this;
    }
};
console.log(obj.a().b()); // 输出a 输出b 输出this指向与obj这个对象

// 下面我们再看下 上面的通过Function扩展类型添加方法的demo如下：
Function.prototype.method = function(name,func) {
    if(!this.prototype[name]) {
        this.prototype[name] = func;
        return this;
    }
}

String.method('trim',function(){
    return this.replace(/^\s+|\s+$/g,'');
});
String.method('log2',function(){
    console.log("链式调用");
    return this;
});
String.method('r',function(){
    return this.replace(/a/,'');
});
var str = " abc ";
console.log(str.trim().log2().r()); // 输出链式调用和 bc
```

**六：理解使用函数实现历史记录--提高性能**

函数可以使用对象去记住先前操作的结果，从而避免多余的运算。比如我们现在测试一个费波纳茨的算法，我们可以使用递归函数计算fibonacci数列，一个fibonacci数字是之前两个fibonacci数字之和，最前面的两个数字是0和1；代码如下：

```
var count = 0;
 var fibonacci = function(n) {
    count++;
    return n < 2 ? n : fibonacci(n-1) + fibonacci(n-2);
 };
 for(var i = 0; i <= 10; i+=1) {
    console.log(i+":"+fibonacci(i));
 }
 console.log(count); // 453
```

我们可以看到如上 fibonacci函数总共调用了453次，for循环了11次，它自身调用了442次，如果我们使用下面的记忆函数的话，那么就可以减少他们的运算次数，从而提高性能；

思路：先使用一个临时数组保存存储结果，当函数被调用的时候，先看是否已经有存储结果 如果有的话，就立即返回这个存储结果，否则的话，调用函数运算下；代码如下：

```
var count2 = 0;
var fibonacci2 = (function(){
    var memo = [0,1];
    var fib = function(n) {
        var result = memo[n];
        count2++;
        if(typeof result !== 'number') {
            result = fib(n-1) + fib(n-2);
            memo[n] = result;
        }
        return result;
    };
    return fib;
 })();
 for(var j = 0; j <= 10; j+=1) {
    console.log(j+":"+fibonacci2(j));
 }
 console.log(count2); // 29
```

这个函数也返回了同样的结果，但是只调用了函数29次，循环了11次，也就是说函数自身调用了18次，从而减少无谓的函数的调用及运算，下面我们可以把这个函数进行抽象化，以构造带记忆功能的函数，如下代码：

```
var count3 = 0;
var memoizer = function(memo,formula) {
    var recur = function(n) {
        var result = memo[n];
        count3++;   // 这句代码只是说明运行函数多少次，在代码中并无作用，实际使用上可以删掉
        if(typeof result !== 'number') {
            result = formula(recur,n);
            memo[n] = result;
        }
        return result;
    };
    return recur;
 };
 var fibonacci3 = memoizer([0,1],function(recur,n){
    return recur(n-1) + recur(n-2);
 });
 // 调用方式如下
 for(var k = 0; k <=10; k+=1) {
    console.log(k+":"+fibonacci3(k));
 }
 console.log(count3); // 29
```

如上封装 memoizer 里面的参数是实现某个方法的计算公式，具体的可以根据需要自己手动更改，这边的思路无非就是想习惯使用对象去保存临时值，从而减少不必要的取值存储值的操作；

**七：理解通过Function扩展类型**

javascript 允许为语言的基本数据类型定义方法。通过`Object.prototype`添加原型方法，该方法可被所有的对象使用。

这对函数，字符串，数字，正则和布尔值都适用，比如如下现在给`Function.prototype`增加方法，使该方法对所有函数都可用，代码如下：

```
Function.prototype.method = function(name,func) {
    if(!this.prototype[name]) {
        this.prototype[name] = func;
        return this;
    }
}
Number.method('integer',function(){
    return Math[this < 0 ? 'ceil' : 'floor'](this);
});
console.log((-10/3).integer()); // -3

String.method('trim',function(){
    return this.replace(/^\s+|\s+$/g,'');
});
console.log(" abc ".trim()); // abc
```

**八：理解使用模块模式编写代码**

使用函数和闭包可以构建模块，所谓模块，就是一个提供接口却隐藏状态与实现的函数或对象。使用函数构建模块的优点是：减少全局变量的使用；

比如如下：我想为String扩展一个方法，该方法的作用是寻找字符串中的HTML字符字体并将其替换为对应的字符；

```
// 如下代码：
Function.prototype.method = function(name,func) {
    if(!this.prototype[name]) {
        this.prototype[name] = func;
        return this;
    }
}
String.method('deentityify',function(){
    var entity = {
        quot: '"',
        It: '<',
        gt: '>'
    };
    return function(){
        return this.replace(/&([^&;]+);/g,function(a,b){
            var r = entity[b];
            return typeof r === 'string' ? r : a;
        });
    }
}());
console.log("&It;">".deentityify()); // <">
```

模块模式利用函数作用域和闭包来创建绑定对象与私有成员的关联，比如在上面的deentityify()方法才有权访问字符实体表entity这个数据对象；

模块开发的一般形式是：定义了私有变量和函数的函数，利用闭包创建可以访问到的私有变量和函数的特权函数，最后返回这个特权函数，或把他们保存到可以访问的地方。

模块模式一般会结合实例模式使用。javascript的实例就是使用对象字面量表示法创建的。对象的属性值可以是数值或者函数，并且属性值在该对象的生命周期中不会发生变化；比如如下代码属于模块模式：定义了一个私有变量name属性，和一个实例模式(对象字面量obj)并且返回这个对象字面量obj，对象字面量中的方法与私有变量name进行了绑定；

```
// 比如如下经典的模块模式
var MODULE = (function(){
    var name = "tugenhua";
    var obj = {
        setName: function() {
            this.name = name;
        },
        getName: function(){
            return this.name;
        }
    };
    return obj;
})();
MODULE.setName()
console.log(MODULE.getName()); // tugenhua
```

**九：理解惰性实列化**

在页面中javascript初始化执行的时候就实例化类，如果在页面中没有使用这个实列化的对象，就会造成一定的内存浪费和性能损耗；这时候，我们可以使用惰性实列化来解决这个问题，惰性就是把实列化推迟到需要使用它的时候才去做，做到 "按需供应";

```
// 惰性实列化代码如下
var myNamespace = function(){
    var Configure = function(){
        var privateName = "tugenhua";
        var privateGetName = function(){
            return privateName;
        };
        var privateSetName = function(name) {
            privateName = name;
        };
        // 返回单列对象
        return {
            setName: function(name) {
                privateSetName(name);
            },
            getName: function(){
                return privateGetName();
            }
        }
    };
    // 存储Configure实列
    var instance;
    return {
        init: function(){
            // 如果不存在实列，就创建单列实列
            if(!instance) {
                instance = Configure();
            }
            // 创建Configure单列
            for(var key in instance) {
                if(instance.hasOwnProperty(key)) {
                    this[key] = instance[key];
                }
            }
            this.init = null;
            return this;
        }
    }
}();
// 调用方式
myNamespace.init();
var name = myNamespace.getName();
console.log(name); // tugenhua
```

如上代码是惰性化实列代码：它包括一个单体Configure实列，直接返回init函数，先判断该单体是否被实列化，如果没有被实列化的话，则创建并执行实列化并返回该实列化，如果已经实列化了，则返回现有实列；执行完后，则销毁init方法，只初始化一次

**十：推荐分支函数(解决兼容问题的更好的方法)**

分支函数的作用是：可以解决兼容问题if或者else的重复判断的问题，我们一般的做法是：根据兼容的不同写if，else等，这些判断来实现兼容，但是这样明显就有一个缺点，每次执行这个函数的时候，都需要进行if和else的检测，效率明显不高，我们现在使用分支函数来实现当初始化的时候进行一些检测，在之后的运行代码过程中，代码就无需检测了；

```
// 我们先来看看传统的封装ajax请求的函数
//创建XMLHttpRequest对象：
var xmlhttp;
function createxmlhttp(){
    if (window.XMLHttpRequest){
        // code for IE7+, Firefox, Chrome, Opera, Safari
        xmlhttp=new XMLHttpRequest();
    }
    else{
      // code for IE6, IE5
      xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
    }
}
// 下面我们看看分支函数代码如下：
var XHR = (function(){
    var standard = {
        createXHR : function() {
            return new XMLHttpRequest();
        }
    };
    var oldActionXObject = {
        createXHR : function(){
            return new ActiveXObject("Microsoft.XMLHTTP");
        }
    };
    var newActionXObject = {
        createXHR : function(){
            return new ActiveXObject("Msxml2.XMLHTTP");
        }
    };
    if(standard.createXHR) {
        return standard;
    }else {
        try{
            newActionXObject.createXHR();
            return newActionXObject;
        }catch(e){
            oldActionXObject.createXHR();
            return oldActionXObject;
        }
    }
})();
console.log(XHR.createXHR()); //xmlHttpRequest对象
```

上面的代码就是分支函数，分支的原理是：声明几个不同名称的对象，且为该不同名称对象声明一个相同的方法，然后根据不同的浏览器设计来实现，接着开始进行浏览器检测，并且根据浏览器检测来返回哪一个对象，不论返回的是哪一个对象，最后它一致对外的接口都是createXHR方法的；

**十一：惰性载入函数（也是解决兼容问题的）**

和上面分支的原理是一样的，代码也可以按照上面的推荐分支风格编码的；解决的问题也是解决多个if条件判断的；代码如下：

```
// 代码如下：
var addEvent = function(el,type,handler){
    addEvent = el.addEventListener ? function(el,type,handler){
        el.addEventListener(type,handler,false);
    } : function(el,type,handler) {
        el.attachEvent("on" + type,handler);
    }
    addEvent(el,type,handler);
};
```

惰性载入函数也是在函数内部改变自身的一种方式，在重复执行的时候就不会再进行检测的；惰性载入函数的分支只会执行一次，即第一次调用的时候，其优点如下：

1.  要执行的适当代码只有在实际调用函数时才执行。

2.  第一次调用该函数的时候，紧接着内部函数也会执行，但是正因为这个，所以后续继续调用该函数的话，后续的调用速度会很快；因此避免了多重条件；

**十二：理解函数节流**

DOM操作的交互需要更多的内存和CPU时间，连续进行过多的DOM相关的操作可能会导致浏览器变慢甚至崩溃，函数节流的设计思想是让某些代码可以在间断的情况下连续重复执行，实现该方法可以使用定时器对该函数进行节流操作;

比如：第一次调用函数的时候，创建一个定时器，在指定的时间间隔下执行代码。当第二次执行的时候，清除前一次的定时器并设置另一个，将其替换成一个新的定时器;

```
// 如下简单函数节流代码演示
var throttle = {
    timeoutId: null,
    // 需要执行的方法
    preformMethod: function(){

    },
    // 初始化需要调用的方法
    process: function(){
        clearTimeout(this.timeoutId);
        var self = this;
        self.timeoutId = setTimeout(function(){
            self.preformMethod();
        },100);
    }
};
// 执行操作
throttle.process();
```

函数节流解决的问题是一些代码(比如事件)无间断的执行，这可能会影响浏览器的性能，比如浏览器变慢或者直接崩溃。比如对于mouseover事件或者click事件，比如点击tab项菜单，无限的点击，有可能会导致浏览器会变慢操作，这时候我们可以使用函数节流的操作来解决；
原文地址：[http://www.cnblogs.com/tugenhua0707/p/5046854.html](http://www.cnblogs.com/tugenhua0707/p/5046854.html)
