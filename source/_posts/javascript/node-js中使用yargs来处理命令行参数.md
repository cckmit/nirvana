---
title: node.js中使用yargs来处理命令行参数
date: 2021-03-11 21:12:40
categories: 前端
---

yargs库能够方便的处理命令行参数。

 

一、安装 yargs

```
npm install yargs --save
```

二、读取命令行参数

```
const yargs = require('yargs');
 
let argv = yargs.argv;
console.log(argv);
```
argv 对象用来保存命令行参数，传递参数时，参数名以 -- 开头，中间使用 = 或 空格，然后接上值 。

argv 有一个 下划线 属性，该属性用来获取非连词线开头的参数
```
const yargs = require('yargs');
 
let argv = yargs.argv;
console.log(argv._);
```
还可以给参数名取一个别名。
```
const yargs = require('yargs');
 
let argv = yargs
    .alias('n', 'name')
    .alias('s', 'save')
    .alias('w', 'width')
    .argv;
 
console.log(argv);
```
.demandOption(key, msg) 是否必须填写
.default(key, value, [description]) 设置默认值
.describe(key, desc) 命令描述信息
```
const yargs = require('yargs');
 
let argv = yargs
    .alias('s', 'src')
    .alias('d', 'dst')
    .demandOption(['s', 'd'], 's 与 d 必须填写')
    .default('s', 'a.txt')
    .default('d', 'b.txt')
    .describe('s', '源文件')
    .describe('d', '目标文件')
    .argv;
 
console.log(argv);
```
我们可以通过 .option(key, [opt]) 配置所有设置
```
const yargs = require('yargs');
 
let argv = yargs
    .option('s', {
        alias: 'src',
        demandOption: true,
        default: 'a.txt',
        describe: '源文件',
        type: 'string'
    })
    .option('d', {
        alias: 'dst',
        demandOption: true,
        default: 'b.txt',
        describe: '目标文件',
        type: 'string'
    })
    .argv;
 
console.log(argv);
```
有些时候我们只想知道某些参数有没有设置，是 true 或是 false。
通过 .boolean(key) 解析参数为布尔值。
```
const yargs = require('yargs');
 
let argv = yargs
    .alias('s', 'save')
    .boolean(['s'])
    .argv;
 
console.log(argv);
```
.usage() 用法格式
.example(cmd, desc) 提供例子
.help() 帮助信息
.epilog(str) 在帮助信息尾部显示
```
const yargs = require('yargs');
 
let argv = yargs
    .alias('s', 'save')
    .usage('Usage: --s <filename>')
    .example('--s a.txt', '设置源文件')
    .epilog('copyright')
    .help('info')
    .argv;
 
console.log(argv);
```

