---
title: typeScript Decorators 的this作用域
date: 2021-03-11 21:12:40
categories: 前端
---

```
class FooBar {
    @log
    public foo(arg): void { 
        console.log(this);
        this.bar(arg);
    }
    @log
    private bar(arg) : void { 
        console.log(this, "bar", arg);
    }
}
```

```
function log(target: Function, key: string, value: any) {
    return {
        value: (...args: any[]) => {
            var a = args.map(a => JSON.stringify(a)).join();
            var result = value.value.apply(this, args); // How to avoid hard coded this?
            var r = JSON.stringify(result);
            console.log(`Call: ${key}(${a}) => ${r}`);
            return result;
        }
    };
}
```

这个时候会提示**Uncaught TypeError: this.bar is not a function**.
这是由于log中使用了箭头函数，改变了方法的作用域。this对象指向的是当前函数的上下文，而不是调用log方法的this对象。这边不要使用箭头函数

```
function log(target: Object, key: string, value: any) {
    return {
        value: function(...args: any[]) {
            var a = args.map(a => JSON.stringify(a)).join();
            var result = value.value.apply(this, args);
            var r = JSON.stringify(result);
            console.log(`Call: ${key}(${a}) => ${r}`);
            return result;
        }
    };
}
```

建议编辑descriptor/value 参数作为返回值。因此我们暂存了 descriptor 中当前value，避免覆盖其他注解对这个 descriptor 可能的修改。

```
function log(target: Object, key: string, descriptor: TypedPropertyDescriptor<any>) {
    var originalMethod = descriptor.value;

    descriptor.value = function(...args: any[]) {
        var a = args.map(a => JSON.stringify(a)).join();
        var result = originalMethod.apply(this, args);
        var r = JSON.stringify(result);
        console.log(`Call: ${key}(${a}) => ${r}`);
        return result;
    };

    return descriptor;
}
```

请参看 [Stack-Overflow 上的答案](https://link.zhihu.com/?target=http%3A//stackoverflow.com/questions/30329832/how-to-avoid-hard-coded-this-in-decorators%23answer-30330602) 了解上面两个问题的更多信息
