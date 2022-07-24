---
title: socke-io-emit-方法
date: 2021-03-11 21:12:40
categories: 前端
---

emit可以很方便的发送数据,例如：

socket.emit('action');表示发送了一个action命令，命令是字符串的，在另一端接收时，可以这么写： socket.on('action',function(){...});

socket.emit('action',data);表示发送了一个action命令，还有data数据，在另一端接收时，可以这么写： socket.on('action',function(data){...});

socket.emit(action,arg1,arg2); 表示发送了一个action命令，还有两个数据，在另一端接收时，可以这么写： socket.on('action',function(arg1,arg2){...});

socket.emit(action,arg1,arg2,arg3,arg4); 如果是多个参数，就也应该知道什么意思了，同上

在emit方法中包含回调函数，例如：

socket.emit('action',data, function(arg1,arg2){...} );那么这里面有一个回调函数可以在另一端调用，另一端可以这么写：socket.on('action',function(data,fn){   fn('a','b') ;  });

上面的data数据可以有0个或者多个，相应的在另一端改变function中参数的个数即可，function中的参数个数和顺序应该和发送时一致

上面的fn表示另一个端传递过来的参数，是个函数，写fn('a','b') ;会回调函数执行。一次发送不应该写多个回调，否则只有最后一个起效，回调应作为最后一个参数。

例子：一个js文件(内容是下面的服务器端)，同一目录下放一个helloworld.html文件(内容是下面的客户端)，启动服务器，访问http://127.0.0.1/

客户端：
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
 <head>
  <title> New Document </title>
  <meta name="Generator" content="EditPlus">
  <meta name="Author" content="">
  <meta name="Keywords" content="">
  <meta name="Description" content="">
 </head>
	<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io.connect('ws://127.0.0.1/');
  socket.emit('msg','You get my message?',function(data){
    alert(data);
   }); 
</script>
 <body>
 
 </body>
</html>
```
服务器端： 
```
var server=require('http').createServer(handler); 
var io = require('socket.io').listen(server);
var fs=require('fs');
server.listen(80);
function handler(req,res){
	fs.readFile(__dirname+'/helloworld.html',
	function(err, data){
		if(err){
			res.writeHead(500);
			return res.end('Error loading index.html');
		}
		res.writeHead(200);
		res.end(data);
	});
}
io.sockets.on('connection', function (socket) {
	  socket.on('msg', function (data,fn) {
		console.log('received message from client:'+data);
		fn('The server has been successfully received your message:'+data);
	  }); 
});
```
