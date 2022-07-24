---
title: WEB前端开发者常用的代码
date: 2021-03-11 21:12:40
categories: 前端
---

1、将彻底屏蔽鼠标右键
```
oncontextmenu="window.event.returnValue=false" 
<table border oncontextmenu=return(false)><td>no</table> //可用于 Table
```
2、取消选取、防止复制
```
<body onselectstart="return false">
```
3.JS不允许粘贴
```
<body onpaste="return false">
```
4、JS防止复制
```
<body oncopy="return false;" oncut="return false;">
```
5、IE 地址栏前换成自己的图标
```
<link rel="Shortcut Icon" href="favicon.ico">
//在文件的根目录放进去这个图片，后缀修改成ico就可以了
```
6.可以在收藏夹中显示出你的图标
  ```
<link rel="Bookmark" href="favicon.ico">
```
7.关闭输入法
```
<input style="ime-mode:disabled">
```
8、永远都会带着框架
```
<script language="JavaScript"><!– if (window == top)top.location.href = “frames.htm"; //frames.htm 为框架网页 // –></script>
```
9、防止被人 frame
```
<SCRIPT LANGUAGE=JAVASCRIPT><!– if (top.location != self.location)top.location=self.location; // –></SCRIPT>
```
10、网页将不能被另存为
```
<noscript><iframe src=*.Html></iframe></noscript>
```
11、查看网页源代码
```
<input type=button value=“查看网页源代码"onclick="window.location = “view-source:"+ “http://www.pconline.com.cn"">
```
12、光标是停在文本框文字的最后
```
<script language="javascript"> 
function cc() { 
      var e = event.srcElement; 
      var r =e.createTextRange();
       r.moveStart(“character",e.value.length); 
      r.collapse(true); 
      r.select(); 
}
 </script> 
<input type=text name=text1 value="123″ onfocus="cc()">
```
13、判断上一页的来源
```
javascript: document.referrer
```
14、最小化、最大化、关闭窗口
```
<object id=hh1 classid="clsid:ADB880A6-D8FF-11CF-9377-00AA003B7A11″> <param name="Command" value="Minimize"></object> <object id=hh2 classid="clsid:ADB880A6-D8FF-11CF-9377-00AA003B7A11″> <param name="Command" value="Maximize"></object> <OBJECT id=hh3 classid="clsid:adb880a6-d8ff-11cf-9377-00aa003b7a11″> <PARAM NAME="Command" VALUE="Close"></OBJECT> <input type=button value=最小化 onclick=hh1.Click()> <input type=button value=最大化 onclick=hh2.Click()> <input type=button value=关闭 onclick=hh3.Click()> 本例适用于 IE
```
15.屏蔽功能键 Shift,Alt,Ctrl
```
<script> 
function look(){
   if(event.shiftKey) 
  alert(“禁止按 Shift 键!"); 
  //可以换成 ALT CTRL
} 
document.onkeydown=look;
 </script>
```
16、网页不会被缓存
```
<META HTTP-EQUIV="pragma" CONTENT="no-cache"> 
<META HTTP-EQUIV="Cache-Control" CONTENT="no-cache, must-revalidate"> 
<META HTTP-EQUIV="expires" CONTENT="Wed, 26 Feb 1997 08:21:57 GMT"> //或者<META HTTP-EQUIV="expires" CONTENT="0″>
```
17、怎样让表单没有凹凸感？
```
<input type=text style="border:1 solid #000000″> 或 <input type=text style="border-left:none; border-right:none; border-top:none; border-bottom: 1 solid #000000″></textarea>
```
18.div span layer的区别？
```
<div>(division)用来定义大段的页面元素，会产生转行 <span>用来定义同一行内的元素，跟<div>的唯一区别是不产生转行 <layer>是 ns 的标记，ie 不支持，相当于<div>
```
19.让弹出窗口总是在最上面:
```
<body onblur="this.focus();">
```
20.不要滚动条?
```
//让竖条没有: 
<body style="overflow:scroll;overflow-y:hidden"> </body> 
///让横条没有: 
<body style="overflow:scroll;overflow-x:hidden"> </body> 
//两个都去掉
 <body scroll="no"> </body>
```
21.怎样去掉图片链接点击后，图片周围的虚线？
```
<a href="#" onFocus="this.blur()"><img src="logo.jpg" border=0></a>
```
22.电子邮件处理提交表单
```
<form name="form1″ method="post" action="mailto:****@***.com" enctype="text/plain"> 
      <input type=submit>
 </form>
```
23.在打开的子窗口刷新父窗口的代码里如何写？
```
window.opener.location.reload()   
```
 24.如何设定打开页面的大小 
```
<body onload="top.resizeTo(300,200);">
 //打开页面的位置
<body onload="top.moveBy(300,200);">
```
25.在页面中如何加入不是满铺的背景图片,拉动页面时背景图不动
```
<STYLE> body {background-image:none; background-repeat:no-repeat; background-position:center;background-attachment: fixed} </STYLE>
```
26、检查一段字符串是否全由数字组成
```
function checkNum(str){
  return str.match(\/D/)==null
}
alert(checkNum("1232142141")) 
alert(checkNum("123214214a1"))
```
27、获得一个窗口的大小
```
document.body.clientWidth; document.body.clientHeight
```
28、怎么判断是否是字符
```
if (/[^/x00-/xff]/g.test(s)) alert(“含有汉字"); else alert(“全是字符");
```
29、TEXTAREA 自适应文字行数的多少
```
<textarea rows=1 name=s1 cols=27 onpropertychange="this.style.posHeight=this.scrollHeight"> </textarea>
```
30、日期减去天数等于第二个日期
```
<script language=Javascript> 
function cc(dd,dadd) { 
    //可以加上错误处理 
    var a = new Date(dd) 
    a = a.valueOf() 
    a = a - dadd * 24 * 60 * 60 * 1000 
    a = new Date(a)         
    alert(a.getFullYear() + "年" + (a.getMonth() + 1) + "月" + a.getDate() + "日")
 }  
cc(“12/23/2002",2) 
</script>
```
31、ENTER 键可以让光标移到下一个输入框
```
<input onkeydown="if(event.keyCode==13)event.keyCode=9″>
```
32、各种样式的光标
```
auto ：标准光标 default ：标准箭头
hand ：手形光标
wait ：等待光标
text ：I 形光标
vertical-text ：水平 I 形光标
no-drop ：不可拖动光标
not-allowed ：无效光标
help ：?帮助光标
all-scroll ：三角方向标
move ：移动标
crosshair ：十字标
e-resize
n-resize
nw-resize
w-resize
s-resize
se-resize
sw-resize
```
