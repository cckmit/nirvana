---
title: 前端实用的调试命令console使用
date: 2021-03-11 21:12:40
categories: 前端
---

## 一、显示信息的命令
```
console.log('hello'); 
console.info('信息');
console.error('错误');
console.warn('警告');
```
![](https://upload-images.jianshu.io/upload_images/10024246-1ed692616e9ad924.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最常用的就是console.log了。

## 二：占位符
console支持printf的占位符格式，支持的占位符有：字符（%s）、整数（%d或%i）、浮点数（%f）和对象（%o），
```
console.log("%d年%d月%d日",2018,04,08);
```
![](https://upload-images.jianshu.io/upload_images/10024246-ec167f3d362eae60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## #三：颜色
console支持打印是显示不同文字，甚至图片，%c:css样式(部分浏览器不支持)

%c：Formats the output string according to CSS styles you provide.
```
console.log("%c3D Text"," text-shadow: 0 1px 0 #ccc,0 2px 0 #c9c9c9,0 3px 0 #bbb,0 4px 0 #b9b9b9,0 5px 0 #aaa,0 6px 1px rgba(0,0,0,.1),0 0 5px rgba(0,0,0,.1),0 1px 3px rgba(0,0,0,.3),0 3px 5px rgba(0,0,0,.2),0 5px 10px rgba(0,0,0,.25),0 10px 10px rgba(0,0,0,.2),0 20px 20px rgba(0,0,0,.15);font-size:5em")
console.log("%cColorful CSS","background: rgba(252,234,187,1);background: -moz-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%,rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: -webkit-gradient(left top, right top, color-stop(0%, rgba(252,234,187,1)), color-stop(12%, rgba(175,250,77,1)), color-stop(28%, rgba(0,247,49,1)), color-stop(39%, rgba(0,210,247,1)), color-stop(51%, rgba(0,189,247,1)), color-stop(64%, rgba(133,108,217,1)), color-stop(78%, rgba(177,0,247,1)), color-stop(87%, rgba(247,0,189,1)), color-stop(100%, rgba(245,22,52,1)));background: -webkit-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: -o-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: -ms-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: linear-gradient(to right, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#fceabb', endColorstr='#f51634', GradientType=1 );font-size:5em")
console.log("%c", "padding:50px 300px;line-height:120px;background:url('https://cdn2.jianshu.io/assets/web/nav-logo-4c7bbafe27adc892f3046e6978459bac.png') no-repeat;");
```
![](https://upload-images.jianshu.io/upload_images/10024246-6a5b5b1cea9583ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 四：信息分组
```
console.group("第一组信息");
	console.log("group1：test1");
	console.log("group1：test2");
console.groupEnd();

console.group("第二组信息");
	console.log("group2：test1");
	console.log("group2：test2");
console.groupEnd();
```
![](https://upload-images.jianshu.io/upload_images/10024246-cced9b0810322c01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 五：查看对象的信息
console.dir()可以显示一个对象所有的属性和方法。
```
var test ={
  name:'test',
  age:18,
  des:'I am Test',
  fn:function(){
    
  }
}
console.dir(test);
```
![](https://upload-images.jianshu.io/upload_images/10024246-a299eabfd7a933ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 六：显示Html页面节点内容

```
    <!DOCTYPE html>
    <html>
    <head>
        <title>console.dirxml()</title>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    </head>
    <body>
        <div id="TZA4S">
            <div class="" id="lga">
                  <img style="padding-top:112px" height="92" src="/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png" width="272" alt="Google" id="hplogo" title="Google" onload="typeof google==='object'&amp;&amp;google.aft&amp;&amp;google.aft(this)">
            </div>      
        </div>
      <script>
         var info = decument.getElementById('TZA4S');
         console.dirxml(info);
      </script>
  </body>
   </html>
```
![](https://upload-images.jianshu.io/upload_images/10024246-72be280b8126019e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 七：断言判断数据或结果
console.assert()用来判断一个表达式或变量是否为真。如果结果为否，则在控制台输出一条相应信息，并且抛出一个异常。
```
var result = 1; 
console.assert( result );
var age = 18; 
console.assert(age === '18' );
```
![](https://upload-images.jianshu.io/upload_images/10024246-779b2a8a366f9c5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 八：打印函数调用的栈信息
调试JavaScript程序时，有时需要打印函数调用的栈信息，这可以通过使用console.trace()来实现
```
function doTask(){
    doSubTask(1000,10000);
}
 
function doSubTask(countX,countY){
    for(var i=0;i<countX;i++){
        for(var j=0;j<countY;j++){} 
    }
    console.trace();
}
doTask();
```
  ![](https://upload-images.jianshu.io/upload_images/10024246-5b43a7ab562c5959.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 九：测量程序消耗的时间
console.time和console.timeEnd这两个方法可以用来让WEB开发人员测量一个javascript脚本程序执行消耗的时间。

console.time方法是开始计算时间，console.timeEnd是停止计时，输出脚本执行的时间。
```
console.time('计时统计：begin')
for(var i=0;i<1000;i++){
  for(var j=0;j<1000;j++){} 
}
console.timeEnd('计时统计：begin')
```
![](https://upload-images.jianshu.io/upload_images/10024246-1e26129602eed7dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 >注意:开始和结束的描述会一直，不然会无法统计，并报警：Timer 'begin' does not exist
本地测试结果如下，但是多尝试几次之后，发现其实每次执行的结果略微会有点误差，而且总体次数越多时间越快。
原因：很多程序、进程、线程都由操作系统控制，会影响执行时间。


还有计时工具performance.now()，node测试代码执行时间process.hrtime();

## 十：性能分析
性能分析（Profiler）就是分析程序各个部分的运行时间，找出瓶颈所在，使用的方法是console.profile()。
console.profile()的使用非常简单：在需要开始profile的地方插入console.profile()，在结束profile的地方插入console.profileEnd()即可。
```
function doTask(){
    doSubTaskA(1000);
    doSubTaskA(100000);
    doSubTaskB(10000);
    doSubTaskC(1000,10000);
}
function doSubTaskA(count){
    for(var i=0;i<count;i++){}
}
 
function doSubTaskB(count){
    for(var i=0;i<count;i++){}
}
 
function doSubTaskC(countX,countY){
    for(var i=0;i<countX;i++){
        for(var j=0;j<countY;j++){} 
    }
}
console.profile();
doTask();
console.profileEnd();
```
![](https://upload-images.jianshu.io/upload_images/10024246-a0a1594caf107dfb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
