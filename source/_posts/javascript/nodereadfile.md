---
title: Nodejs读取目录下的文件并编辑
date: 2021-04-21 14:27:21
categories: 前端
---
node下读取和编辑文件都是使用`fs`组件
## 引用模块
```
const fs = require('fs');
```
## 读取文件夹
```
/**
* path - 文件路径。
* callback - 回调函数，回调函数带有两个参数err, files，err 为错误信息，files 为 目录下的文件数组列表。
**/

fs.readdir(path, callback)
#异步 readdir(3). 读取目录的内容。
fs.readdirSync(path)
#同步 readdir().返回文件数组列表。

//demo
const fs = require('fs');
const path = require('path');
fs.readdir(pathName, function(err, files){
    var dirs = [];
    (function iterator(i){
      if(i == files.length) {
        console.log(dirs);
      }
      fs.stat(path.join(pathName, files[i]), function(err, data){     
        if(data.isFile()){               
            dirs.push(files[i]);
            iterator(i+1);
       });   
    })(0);
});
```

## 编辑文件
```
var fs = require("fs");

// 异步读取
fs.readFile('input.txt', function (err, data) {
   if (err) {
       return console.error(err);
   }
   console.log("异步读取: " + data.toString());
});

// 同步读取
var data = fs.readFileSync('input.txt');
console.log("同步读取: " + data.toString());

console.log("程序执行完毕。");
```
