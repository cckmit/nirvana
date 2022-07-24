---
title: tsconfig.json配置参数
date: 2022-06-25 21:12:40
categories: 前端
tags: [TypeScript]
---
{
  //tsconfig.json是ts编译器的配置文件,ts编译器可以根据它的信息来对代码进行编译
  //include 用来指定哪些ts文件被编译
  //**代表scr下的任意目录,*代表任意文件
  //exclude表示不包含.一般不设置.会有默认值
  //extends定义被继承的配置文件
  // files指定被编译文件的列表
  "include": [
    "./src/**/*"
  ],
  "exclude": [
    "./src/test/**/*"
  ],
  // "extends": ""
  // "files": [
  //   //文件名字
     //01_变量.ts
  // ]
  // compilerOptions 编译器的配置选项
  "compilerOptions": {

    //target 用来指定ts被编译为的ES版本.默认是被编译为ES3
    //target' option must be: 'es3', 'es5', 'es6', 'es2015', 'es2016', 'es2017', 'es2018', 'es2019', 'es2020', 'es2021', 'esnext'.
     "target": "ES3",

     //module 模块化,指定要使用的模块化规范
     "module": "es2015",

     //lib,library库的简称.用来指定项目中使用的库.一般使用默认值就可以
    //  "lib": ["es6","dom"]

    //outDir 用来指定编译后文件所在的目录
    "outDir": "./dist",

    // 设置outFile后,所有的全局作用域中的代码会合并到同一个文件中
    // "outFile": "./dist/app.js"
    // allowJs是否对js文件进行编译,默认是false
    "allowJs": false,

    // checkJs是否检查js代码复合语法规范,默认值是false
    "checkJs": false,

    //removeComments是否移除注释
    "removeComments": false,

    //noEmit 不生成编译后的文件
    "noEmit": false,

    //noEmitOnError 当有错误的时候不能生成编译后的文件
    "noEmitOnError": false,

    //strict所有严格检查的总开关
    "strict": false,

    //alwaysStrict 用来设置编译后的文件是否使用严格模式.默认false
    "alwaysStrict": false,

    //是否允许隐式的any类型
    "noImplicitAny": false,

    // noImplicitThis 是否允许不明确类型的this
    "noImplicitThis": false,
   
    // strictNullChecks是否严格的检查空值
    "strictNullChecks": false,

  }
}
