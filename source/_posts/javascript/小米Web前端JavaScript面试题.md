---
title: 小米Web前端JavaScript面试题
date: 2021-06-10 16:32:11
categories: 前端
---
一、
请定义这样一个函数

```
function repeat (func, times, wait) {
}
```
这个函数能返回一个新函数，比如这样用
var repeatedFun = repeat(alert, 10, 5000)
调用这个 repeatedFun ("hellworld")
会alert十次 helloworld, 每次间隔5秒
二、
写一个函数stringconcat， 要求能
```
var result1 = stringconcat("a", "b")  result1 = "a+b"
var stringconcatWithPrefix = stringconcat.prefix("hellworld");
var result2 = stringconcatWithPrefix("a", "b")  result2 = "hellworld+a+b"
```

/**
 * 第一题
 * @param func
 * @param times
 * @param wait
 * @returns {repeatImpl}
 */
function repeat (func, times, wait) {
    //不用匿名函数是为了方便调试
    function repeatImpl(){
        var handle,
            _arguments = arguments,
            i = 0;
        handle = setInterval(function(){
            i = i + 1;
            //到达指定次数取消定时器
            if(i === times){
                clearInterval(handle);
                return;
            }
            func.apply(null, _arguments);
        },wait);
    }

    return repeatImpl;
}

//测试用例
var repeatFun = repeat(alert, 4, 3000);

repeatFun("hellworld");


/**
 * 第二题
 * @returns {string}
 */
function stringconcat(){
    var result = [];

    stringconcat.merge.call(null, result, arguments);
    return result.join("+");
}

stringconcat.prefix = function(){
    var _arguments = [],
        _this = this;

    _this.merge.call(null, _arguments, arguments);

    return function(){
        var _args = _arguments.slice(0);

        _this.merge.call(null, _args, arguments);
        return _this.apply(null, _args);
    };
};

stringconcat.merge = function(array, arrayLike){
    var i = 0;

    for(i = 0; i < arrayLike.length; i++){
        array.push(arrayLike[i]);
    }
}


//测试用例
var result1 = stringconcat("a", "b"); //result1 = "a+b"
var result3 = stringconcat("c", "d"); //result1 = "a+b"

var stringconcatWithPrefix = stringconcat.prefix("hellworld");
var stringconcatWithPrefix1 = stringconcat.prefix("hellworld1");

var result2 = stringconcatWithPrefix("a", "b"); //result2 = "hellworld+a+b"
var result4 = stringconcatWithPrefix1("c", "d"); //result2 = "hellworld+a+b"

alert(result1);
alert(result2);
alert(result3);
alert(result4);
