---
title: inquirer.js-——-用户与命令行交互的工具
date: 2021-03-11 21:12:40
categories: 前端
---

`Inquirerjs `是一个用来实现命令行交互式界面的工具集合。它帮助我们实现与用户的交互式交流，比如给用户提一个问题，用户给我们一个答案，我们根据用户的答案来做一些事情
由于交互的问题种类不同，inquirer为每个问题提供很多参数：

- type：表示提问的类型，包括：input, confirm, list, rawlist, expand, checkbox, password, editor；
- name: 存储当前问题回答的变量；
- message：问题的描述；
- default：默认值；
- choices：列表选项，在某些type下可用，并且包含一个分隔符(separator)；
- validate：对用户的回答进行校验；
- filter：对用户的回答进行过滤处理，返回处理后的值；
- transformer：对用户回答的显示效果进行处理(如：修改回答的字体或背景颜色)，但不会影响最终的答案的内容；
- when：根据前面问题的回答，判断当前问题是否需要被回答；
- pageSize：修改某些type类型下的渲染行数；
- prefix：修改message默认前缀；
- suffix：修改message默认后缀。
### 一. 使用

#### 0\. 语法结构

```
const inquirer = require('inquirer');

const promptList = [
    // 具体交互内容
];

inquirer.prompt(promptList).then(answers => {
    console.log(answers); // 返回的结果
})
```

#### 1\. input

```
const promptList = [{
    type: 'input',
    message: '设置一个用户名:',
    name: 'name',
    default: "test_user" // 默认值
},{
    type: 'input',
    message: '请输入手机号:',
    name: 'phone',
    validate: function(val) {
        if(val.match(/\d{11}/g)) { // 校验位数
            return val;
        }
        return "请输入11位数字";
    }
}];
```

效果：
![input](https://upload-images.jianshu.io/upload_images/10024246-a61db2cc44656c1a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2\. confirm

```
const promptList = [{
    type: "confirm",
    message: "是否使用监听？",
    name: "watch",
    prefix: "前缀"
},{
    type: "confirm",
    message: "是否进行文件过滤？",
    name: "filter",
    suffix: "后缀",
    when: function(answers) { // 当watch为true的时候才会提问当前问题
        return answers.watch
    }
}];
```

效果：
![confirm_y](https://upload-images.jianshu.io/upload_images/10024246-2cdda6a4350f38d7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![confirm_n](https://upload-images.jianshu.io/upload_images/10024246-90ec32a80e5c45fd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3\. list

```
const promptList = [{
    type: 'list',
    message: '请选择一种水果:',
    name: 'fruit',
    choices: [
        "Apple",
        "Pear",
        "Banana"
    ],
    filter: function (val) { // 使用filter将回答变为小写
        return val.toLowerCase();
    }
}];
```

效果：
![list_1](https://upload-images.jianshu.io/upload_images/10024246-dfa4dc6a673edce2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![list](https://upload-images.jianshu.io/upload_images/10024246-59be0f369c690cd1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4\. rawlist

```
const promptList = [{
    type: 'rawlist',
    message: '请选择一种水果:',
    name: 'fruit',
    choices: [
        "Apple",
        "Pear",
        "Banana"
    ]
}];
```

效果：
![rawlist](https://upload-images.jianshu.io/upload_images/10024246-5a43083f7a9bac09?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5\. expand

```
const promptList = [{
    type: "expand",
    message: "请选择一种水果：",
    name: "fruit",
    choices: [
        {
            key: "a",
            name: "Apple",
            value: "apple"
        },
        {
            key: "O",
            name: "Orange",
            value: "orange"
        },
        {
            key: "p",
            name: "Pear",
            value: "pear"
        }
    ]
}];
```

效果：
![expend_1](https://upload-images.jianshu.io/upload_images/10024246-4399710481c3226c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![expend_2](https://upload-images.jianshu.io/upload_images/10024246-8abcbd27a8c47233?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 6\. checkbox

```
const promptList = [{
    type: "checkbox",
    message: "选择颜色:",
    name: "color",
    choices: [
        {
            name: "red"
        },
        new inquirer.Separator(), // 添加分隔符
        {
            name: "blur",
            checked: true // 默认选中
        },
        {
            name: "green"
        },
        new inquirer.Separator("--- 分隔符 ---"), // 自定义分隔符
        {
            name: "yellow"
        }
    ]
}];
// 或者下面这样
const promptList = [{
    type: "checkbox",
    message: "选择颜色:",
    name: "color",
    choices: [
        "red",
        "blur",
        "green",
        "yellow"
    ],
    pageSize: 2 // 设置行数
}];
```

效果：
![checkbox_sep](https://upload-images.jianshu.io/upload_images/10024246-c96229da119c592d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![checkbox_size](https://upload-images.jianshu.io/upload_images/10024246-9baf91e05a3e5dc1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 7\. password

```
const promptList = [{
    type: "password", // 密码为密文输入
    message: "请输入密码：",
    name: "pwd"
}];
```

效果：
![pwd](https://upload-images.jianshu.io/upload_images/10024246-8e3e714bf4be5ccb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 8\. editor

```
const promptList = [{
    type: "editor",
    message: "请输入备注：",
    name: "editor"
}];
```

效果：
![editor_inset](https://upload-images.jianshu.io/upload_images/10024246-62296d4127f98eb3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![editor_res](https://upload-images.jianshu.io/upload_images/10024246-605305e7f9b0354a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

