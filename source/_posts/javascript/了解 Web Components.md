---
title: 了解 Web Components
date: 2022-04-21 16:28:03
categories: 前端
---
## **Web Components**

首先来了解下 Web Components 的基本概念， Web Component 是指一系列加入 w3c 的 HTML与DOM的特性，目的是为了从原生层面实现组件化，可以使开发者开发、复用、扩展自定义组件，实现自定义标签。

这是目前前端开发的一次重大的突破。**它意味着我们前端开发人员开发组件时，不必关心那些其他MV*框架的兼容性，真正可以做到 “Write once, run anywhere”。**

例如：
```
// 假如我已经构建好一个 Web Components 组件 <hello-world>并导出
// 在 html 页面，我们就可以直接引用组件
<script src="/my-component.js"></script>

// 而在 html 里面我们可以这样使用
<hello-world></hello-word>
```
而且跟任何框架无关，代表着它不需要任何外部 runtime 的支持，也不需要复杂的Vnode算法映射到实际DOM，只是浏览器api本身对标签内部逻辑进行一些编译处理，性能必定会比一些MV*框架要好一些。

那它是怎么做到高性能的呢？主要和它的核心API有关。其实在上篇中我们已经简单提到了 Web Components 的三个核心 API，接下来我带大家详细分析各个api所承担的功能和实际用法，想必了解过 Web Component 核心技术后，大家就不会对它感到陌生了。

## 三个核心API

#### **Custom elements（自定义元素）**

首先来了解下自定义元素，其实它是作为 Web Component 的基石。那么我们来看下这个基石提供了哪些方法，提供给我们进行高楼大厦的建设。

1.  自定义元素挂载方法

自定义元素通过CustomElementRegistry 来自定义可以直接渲染的html元素，挂载在 window.customElements.define 来供开发者调用，demo 如下：

```
// 假如我已经构建好一个 Web Components 组件 <hello-world>并导出

class HelloWorld extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({ mode: 'open' });
        this.shadowRoot.innerHTML = `
           <style>
                :host {
                    display: block;
                    padding: 10px;
                    background-color: #eee;
                }
            </style>
            <h1>Hello World!</h1>
        `;
    }
}

// 挂载
window.customElements.define('hello-world', HelloWorld)

// 然后就可以在 html 中使用
<hello-world></hello-world>
```

注意：自定义元素必须用'-'连接符连接，来作为特定的区分，如果没有检测到自定义元素，则浏览器会作为空div处理。

渲染结果：

![](https://upload-images.jianshu.io/upload_images/10024246-2636502be8e01f6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.  自定义元素的类

由上面的例子 "class HelloWorld *extends* *HTMLElement { xxx }* " 发现，自定义元素的构造都是基于 HTMLElement，所以它继承了 HTML 元素特性，当然，也可以继承 HTMLElement的派生类，如：HTMLButtonElement 等，来作为现有标签的扩展。

3.  自定义元素的生命周期

类似于现有MV*框架的生命周期，自定义元素的基类里面也包含了完整的生命周期 hook 来提供给开发者实现一些业务逻辑的应用：

```
class HelloWorld extends HTMLElement {
    constructor() {
        // 1 构建组件的时候的逻辑 hook
        super();
    }
  // 2 当自定义元素首次被渲染到文档时候调用 
  connectedCallback(){
  } 
  // 3 当自定义元素在文档中被移除调用 
  disconnectedCallback(){ 
  } 
  // 4 当自定义组件被移动到新的文档时调用
  adoptedCallback(){ 
  } 
  // 5 当自定义元素的属性更改时调用
  attributeChangedCallback(){  
  }
}
```

4.  添加自定义方法和属性

由于自定义元素由一个类来构造，所以添加自定义属性和方法就如同平常开发类的方法一致。
```
class HelloWorld extends HTMLElement {
    constructor() {
        super();
    }

    tag = 'hello-world'

    say(something: string) {
        console.log(`hello world, I want to say ${this.tag} ${something}`)
    }
}

// 调用方法如下
const hw = document.querySelector('hello-world'); 
hw.say('good'); 

// 控制台打印效果如下
```

![](https://upload-images.jianshu.io/upload_images/10024246-136d20885f4dd102.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### **Shadow DOM（影子DOM）**

有了自定义元素作为基石，我们想要更加顺畅的进行组件化封装，必定少不了对于DOM树的操作。那么好的，Shadow DOM（影子DOM）就应运而生了。

顾名思义，影子DOM就是用来隔离自定义元素不受到外界样式或者一些副作用的影响，或者内部的一些特性不会影响外部。使自定义元素保持一个相对独立的状态。

在我们日常开发html页面的时候也会接触到一些使用 Shadow DOM 的标签，比如：audio 和 video 等；在具体dom树中它会一一个标签存在，会隐藏内部的结构，但是其中的控件，比如：进度条、声音控制等，都会以一个Shadow DOM存在于标签内部，如果想要查看具体的DOM结构，则可以尝试在chrome的控制台 -> Preferences -> Show user agent Shadow DOM， 就可以查看到内部的结构构成。

如果组件使用Shadow host，常规document中会存在一个 Shadow host节点用来挂载 Shadow DOM，Shadow DOM内部也会存在一个DOM树：Shadow Tree，根节点为Shadow root，外部可以用伪类:host来访问，Shadow boundary其实就是Shadow DOM的边界。具体架构图如下：

![](https://upload-images.jianshu.io/upload_images/10024246-5c66253a6c196e75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面我们通过一个简单的例子来看下Shadow DOM的实际用处：

```
// Shadow DOM 开启方式为

this.attachShadow( { mode: 'open' } );
```

*   **不使用Shadow DOM**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Web Components</title>
    <style> h1 {
            font-size: 20px;
            color: yellow;
        } </style>
  </head>

  <body>
    <div></div>
    <hello-world></hello-world>
    <h1>Hello World! 外部</h1>
    <script type="module"> class HelloWorld extends HTMLElement {
            constructor() {
                super();
                // 关闭 shadow DOM
                // this.attachShadow({ mode: 'open' });

                const d = document.createElement('div');
                const s = document.createElement('style');
                s.innerHTML = `h1 {
                            display: block;
                            padding: 10px;
                            background-color: #eee;
                        }`
                d.innerHTML = `
                    <h1>Hello World! 自定义组件内部</h1>
                `;

                this.appendChild(s);
                this.appendChild(d);
            }

            tag = 'hello-world'

            say(something) {
                console.log(`hello world, I want to say ${this.tag} ${something}`)
            }
        }

        window.customElements.define('hello-world', HelloWorld);
        const hw = document.querySelector('hello-world'); 
        hw.say('good'); </script>
  </body>
</html>
```

渲染效果为，可以看到样式已经互相污染：

![](https://upload-images.jianshu.io/upload_images/10024246-f00ab63e6fb97d41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   **使用 Shadow DOM**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Web Components</title>
    <style> h1 {
            font-size: 20px;
            color: yellow;
        } </style>
  </head>
  <body>
    <div></div>
    <hello-world></hello-world>
    <h1>Hello World! 外部</h1>
    <script type="module"> class HelloWorld extends HTMLElement {
            constructor() {
                super();
                this.attachShadow({ mode: 'open' });
                this.shadowRoot.innerHTML = `
                    <style>
                        h1 {
                            font-size: 30px;
                            display: block;
                            padding: 10px;
                            background-color: #eee;
                        }
                    </style>
                    <h1>Hello World! 自定义组件内部</h1>
                `;
            }

            tag = 'hello-world'

            say(something) {
                console.log(`hello world, I want to say ${this.tag} ${something}`)
            }
        }

        window.customElements.define('hello-world', HelloWorld);
        const hw = document.querySelector('hello-world'); 
        hw.say('good'); </script>
  </body>
</html>
```

渲染结果为：

![](https://upload-images.jianshu.io/upload_images/10024246-960f367884281043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以清晰的看到样式直接互相隔离无污染，这就是Shadow DOM的好处。

#### **HTML templates（HTML模板）**

**template**模板可以说是大家比较熟悉的一个标签了，在Vue项目中的单页面组件中我们经常会用到，但是它也是 Web Components API 提供的一个标签，它的特性就是包裹在 template 中的 HTML 片段不会在页面加载的时候解析渲染，但是可以被 js 访问到，进行一些插入显示等操作。所以它作为自定义组件的核心内容，用来承载 HTML 模板，是不可或缺的一部分。

使用场景如下：

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Web Components</title>
    <style> h1 {
            font-size: 20px;
            color: yellow;
        } </style>
</head>

<body>
    <div></div>
    <hello-world></hello-world>

    <template id="hw"> 
    <style> .box { 
        padding: 20px;
    } 

    .box > .first { 
        font-size: 24px; 
        color: red;
    } 

    .box > .second { 
        font-size: 14px; 
        color: #000;
    } </style> 

    <div class="box"> 
        <p class="first">Hello</p> 
        <p class="second">World</p> 
    </div> 
    </template>

    <script type="module"> class HelloWorld extends HTMLElement { 
            constructor() {
                super(); 
                const root = this.attachShadow({ mode: 'open' });
               root.appendChild(document.getElementById('hw').content.cloneNode(true));
            }
        } 
        window.customElements.define('hello-world', HelloWorld); </script>
</body>

</html>
```

渲染结果为：

![](https://upload-images.jianshu.io/upload_images/10024246-931919e95fb77e6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Slot** 大家应该也比较熟悉了，相当于一个连接组件内部和外部的一个占位机制，可以用来传递 HTML 代码片段，这里我就不过多赘述，有需要继续了解的同学，Google一下即可。

说完了 Web Components 的“三驾马车”，大家一定对于 Web Components 有了深入的了解，也熟悉了 Web Components 一些常规写法。
