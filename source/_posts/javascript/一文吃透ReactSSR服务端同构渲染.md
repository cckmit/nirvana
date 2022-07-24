---
title: 一文吃透React SSR服务端同构渲染
date: 2021-06-10 16:41:24
categories: 前端
---
# 为什么要服务端渲染(ssr)

至于为什么要服务端渲染，我相信大家都有所闻，而且每个人都能说出几点来。

## 首屏等待

在 SPA 模式下，所有的数据请求和 Dom 渲染都在浏览器端完成，所以当我们第一次访问页面的时候很可能会存在“白屏”等待，而服务端渲染所有数据请求和 html内容已在服务端处理完成，浏览器收到的是完整的 html 内容，可以更快的看到渲染内容，在服务端完成数据请求肯定是要比在浏览器端效率要高的多。

## 没考虑seo的感受

有些网站的流量来源主要还是靠搜索引擎，所以网站的 seo 还是很重要的，而 SPA 模式对搜索引擎不够友好，要想彻底解决这个问题只能采用服务端直出。改变不了别人（搜索yinqing），只能改变自己。

## SSR + SPA 体验升级

只实现 SSR 其实没啥意义，技术上没有任何发展和进步，否则 SPA 技术就不会出现。但是单纯的 SPA又不够完美，所以最好的方案就是这两种体验和技术的结合，第一次访问页面是服务端渲染，基于第一次访问后续的交互就是 SPA 的效果和体验，还不影响SEO 效果，这就有点完美了。单纯实现 ssr 很简单，毕竟这是传统技术，也不分语言，随便用 php 、jsp、asp、node 等都可以实现。但是要实现两种技术的结合，同时可以最大限度的重用代码（同构），减少开发维护成本，那就需要采用 react 或者 vue等前端框架相结合 node (ssr) 来实现。

本文主要说 React SSR 技术 ,当然 vue 也一样，只是技术栈不同而已。

# 核心原理

整体来说 react 服务端渲染原理不复杂，其中最核心的内容就是同构。

node server 接收客户端请求，得到当前的req url path,然后在已有的路由表内查找到对应的组件，拿到需要请求的数据，将数据作为 props、context或者store 形式传入组件，然后基于 react 内置的服务端渲染api renderToString() or renderToNodeStream() 把组件渲染为 html字符串或者 stream 流, 在把最终的 html 进行输出前需要将数据注入到浏览器端(注水)，server 输出(response)后浏览器端可以得到数据(脱水)，浏览器开始进行渲染和节点对比，然后执行组件的componentDidMount 完成组件内事件绑定和一些交互，浏览器重用了服务端输出的 html 节点，整个流程结束。

技术点确实不少，但更多的是架构和工程层面的，需要把各个知识点进行链接和整合。这里放一个架构图：

![](https://upload-images.jianshu.io/upload_images/10024246-ba28fc3e201b3ca0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# react ssr

## 从 ejs 开始

实现 ssr 很简单，先看一个 node ejs的栗子。

```
// index.html
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <meta http-equiv="X-UA-Compatible" content="ie=edge">
   <title>react ssr <%= title %></title>
</head>
<body>
   <%=  data %>
</body>
</html>

```

```
 //node ssr
 const ejs = require('ejs');
 const http = require('http');

http.createServer((req, res) => {
    if (req.url === '/') {
        res.writeHead(200, {
            'Content-Type': 'text/html' 
        });
        // 渲染文件 index.ejs
        ejs.renderFile('./views/index.ejs', {
            title: 'react ssr', 
            data: '首页'}, 
            (err, data) => {
            if (err ) {
                console.log(err);
            } else {
                res.end(data);
            }
        })
    }
}).listen(8080);

```

## jsx 到字符串

上面我们结合 ejs模板引擎 ，实现了一个服务端渲染的输出，html 和 数据直接输出到客户端。参考以上，我们结合 react组件 来实现服务端渲染直出，使用 jsx 来代替 ejs，之前是在 html 里使用 ejs 来绑定数据，现在改写成使用jsx 来绑定数据,使用 react 内置 api 来把组件渲染为 html 字符串，其他没有差别。

为什么react 组件可以被转换为 html字符串呢？简单的说我们写的 jsx 看上去就像在写 html（其实写的是对象） 标签，其实经过编译后都会转换成React.createElement方法，最终会被转换成一个对象(虚拟DOM)，而且和平台无关，有了这个对象，想转换成什么那就看心情了。

```
const  React  = require('react');

const { renderToString}  = require( 'react-dom/server');

const http = require('http');

//组件
class Index extends React.Component{
    constructor(props){
        super(props);
    }

    render(){
        return <h1>{this.props.data.title}</h1>
    }
}

//模拟数据的获取
const fetch = function () {
    return {
        title:'react ssr',
        data:[]
    }
}

//服务
http.createServer((req, res) => {
    if (req.url === '/') {
        res.writeHead(200, {
            'Content-Type': 'text/html'
        });

        const data = fetch();

        const html = renderToString(<Index data={data}/>);
        res.end(html);
    }
}).listen(8080);

```

**ps**:以上代码不能直接运行，需要结合babel 使用 @babel/preset-react 进行转换

```
npx babel script.js --out-file script-compiled.js --presets=@babel/preset-react
```

# 引出问题

在上面非常简单的就是实现了 react ssr ,把jsx作为模板引擎，不要小看上面的一小段代码，他可以帮我们引出一系列的问题，这也是完整实现 react ssr 的基石。

*   双端路由如何维护？

首先我们会发现我在 server 端定义了路由 '/'，但是在 react SPA 模式下我们需要使用react-router来定义路由。那是不是就需要维护两套路由呢？

*   获取数据的方法和逻辑写在哪里？

发现数据获取的fetch 写的独立的方法，和组件没有任何关联，我们更希望的是每个路由都有自己的 fetch 方法。

*   服务端 html 节点无法重用

虽然组件在服务端得到了数据，也能渲染到浏览器内，但是当浏览器端进行组件渲染的时候直出的内容会一闪而过消失。

好了，问题有了，接下来我们就一步一步的来解决这些问题。

# 同构才是核心

react ssr 的核心就是同构，没有同构的 ssr 是没有意义的。

所谓同构就是采用一套代码，构建双端（server 和 client）逻辑，最大限度的重用代码，不用维护两套代码。而传统的服务端渲染是无法做到的，react 的出现打破了这个瓶颈，并且现在已经得到了比较广泛的应用。

## 路由同构

双端使用同一套路由规则，node server 通过req url path 进行组件的查找，得到需要渲染的组件。

//组件和路由配置 ，供双端使用 routes-config.js

```
class Detail extends React.Component{
    render(){
        return <div>detail</div>
    }
}

class Index extends React.Component {
    render() {
        return <div>index</div>
    }
}

const routes = [
            {
                path: "/",
                exact: true,
                component: Home
            },
            {
                path: '/detail', exact: true,
                component:Detail,
            },
            {
                path: '/detail/:a/:b', exact: true,
                component: Detail
            } 
];
//导出路由表
export default routes;
```

//客户端 路由组件

```
import routes from './routes-config.js';
function App(){
    return (
        <Layout>
            <Switch>
                        {
                            routes.map((item,index)=>{
                                return <Route path={item.path} key={index} exact={item.exact} render={item.component}></Route>
                            })
                        }
            </Switch>
        </Layout>
    );
}
export default App;
```

**node server 进行组件查找**

路由匹配其实就是对 组件path 规则的匹配，如果规则不复杂可以自己写，如果情况很多种还是使用官方提供的库来完成。

**matchRoutes(routes, pathname)**

```
//引入官方库
import { matchRoutes } from "react-router-config";
import routes from './routes-config.js';

const path = req.path;

const branch = matchRoutes(routes, path);

//得到要渲染的组件
const Component = branch[0].route.component;

//node server 
http.createServer((req, res) => {

        const url = req.url;
        //简单容错，排除图片等资源文件的请求
        if(url.indexOf('.')>-1) { res.end(''); return false;}

        res.writeHead(200, {
            'Content-Type': 'text/html'
        });
        const data = fetch();

        //查找组件
        const branch =  matchRoutes(routes,url);

        //得到组件
        const Component = branch[0].route.component;

        //将组件渲染为 html 字符串
        const html = renderToString(<Component data={data}/>);

        res.end(html);

 }).listen(8080);

```

可以看下matchRoutes方法的返回值,其中route.component 就是 要渲染的组件

```
[
    { 

    route:
        { path: '/detail', exact: true, component: [Function: Detail] },
    match:
        { path: '/detail', url: '/detail', isExact: true, params: {} } 

    }
]

```

react-router-config 这个库由react 官方维护，功能是实现嵌套路由的查找，代码没有多少，有兴趣可以看看。

**文章走到这里，相信你已经知道了路由同构，所以上面的第一个问题 ： 【双端路由如何维护？】 解决了。**

## 数据同构（预取同构）

这里开始解决我们最开始发现的第二个问题 - 【获取数据的方法和逻辑写在哪里？】

数据预取同构，解决双端如何使用同一套数据请求方法来进行数据请求。

先说下流程，在查找到要渲染的组件后，需要预先得到此组件所需要的数据，然后将数据传递给组件后，再进行组件的渲染。

我们可以通过给组件定义静态方法来处理，组件内定义异步数据请求的方法也合情合理，同时声明为静态（static），在 server 端和组件内都也可以直接通过组件（function） 来进行访问。

比如 Index.getInitialProps

```
//组件
class Index extends React.Component{
    constructor(props){
        super(props);
    }

    //数据预取方法  静态 异步 方法
    static async  getInitialProps(opt) {
        const fetch1 =await fetch('/xxx.com/a');
        const fetch2 = await fetch('/xxx.com/b');

        return {
            res:[fetch1,fetch2]
        }
    }

    render(){
        return <h1>{this.props.data.title}</h1>
    }
}

//node server 
http.createServer((req, res) => {

        const url = req.url;
        if(url.indexOf('.')>-1) { res.end(''); return false;}

        res.writeHead(200, {
            'Content-Type': 'text/html'
        });

        //组件查找
        const branch =  matchRoutes(routes,url);

        //得到组件
        const Component = branch[0].route.component;

        //数据预取
        const data = Component.getInitialProps(branch[0].match.params);

        //传入数据，渲染组件为 html 字符串
        const html = renderToString(<Component data={data}/>);

        res.end(html);

 }).listen(8080);

```

另外还有在声明路由的时候把数据请求方法关联到路由中，比如定一个 loadData 方法，然后在查找到路由后就可以判断是否存在loadData这个方法。

看下参考代码

```

const loadBranchData = (location) => {
  const branch = matchRoutes(routes, location.pathname)

  const promises = branch.map(({ route, match }) => {
    return route.loadData
      ? route.loadData(match)
      : Promise.resolve(null)
  })

  return Promise.all(promises)
}

```

上面这种方式实现上没什么问题，但从职责划分的角度来说有些不够清晰，我还是比较喜欢直接通过组件来得到异步方法。

好了，到这里我们的第二个问题 - 【获取数据的方法和逻辑写在哪里？】 解决了。

## 渲染同构

假设我们现在基于上面已经实现的代码，同时我们也使用 webpack 进行了配置，对代码进行了转换和打包，整个服务可以跑起来。

路由能够正确匹配，数据预取正常，服务端可以直出组件的 html ，浏览器加载 js 代码正常，查看网页源代码能看到 html 内容，好像我们的整个流程已经走完。

但是当浏览器端的 js 执行完成后，发现数据重新请求了，组件的重新渲染导致页面看上去有些闪烁。

这是因为在浏览器端，双端节点对比失败，导致组件重新渲染，也就是只有当服务端和浏览器端渲染的组件具有相同的props 和 DOM 结构的时候，组件才能只渲染一次。

刚刚我们实现了双端的数据预取同构，但是数据也仅仅是服务端有，浏览器端是没有这个数据，当客户端进行首次组件渲染的时候没有初始化的数据，渲染出的节点肯定和服务端直出的节点不同，导致组件重新渲染。

### 数据注水

在服务端将预取的数据注入到浏览器，使浏览器端可以访问到，客户端进行渲染前将数据传入对应的组件即可，这样就保证了props的一致。

```

//node server  参考代码
http.createServer((req, res) => {

        const url = req.url;
        if(url.indexOf('.')>-1) { res.end(''); return false;}

        res.writeHead(200, {
            'Content-Type': 'text/html'
        });

        console.log(url);

        //查找组件
        const branch =  matchRoutes(routes,url);
        //得到组件
        const Component = branch[0].route.component;

        //数据预取
        const data = Component.getInitialProps(branch[0].match.params);

        //组件渲染为 html
        const html = renderToString(<Component data={data}/>);

        //数据注水
        const propsData = `<textarea id="krs-server-render-data-BOX">${JSON.stringify(data)}</textarea>`;

        // 通过 ejs 模板引擎将数据注入到页面
        ejs.renderFile('./index.html', {
            htmlContent: html,  
            propsData
        },  // 渲染的数据key: 对应到了ejs中的index
            (err, data) => {
                if (err) {
                    console.log(err);
                } else {
                    console.log(data);
                    res.end(data);
                }
            })

 }).listen(8080);

 //node ejs html

 <!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
</head>

<body>
    <div id="rootEle">
        <%- htmlContent %> //组件 html内容
    </div>

    <%- propsData %> //组件 init  state ，现在是个字符串
</body>

</html>
</body>

```

需要借助 ejs 模板，将数据绑定到页面上，为了防止 XSS攻击，这里我把数据写到了 textarea 标签里。

下图中，我看着明文数据难受，对数据做了base64编码 ，用之前需要转码，看个人需要。
![image](https://upload-images.jianshu.io/upload_images/10024246-4ea344d1832c1cca.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 数据脱水

上一步数据已经注入到了浏览器端，这一步要在客户端组件渲染前先拿到数据，并且传入组件就可以了。

客户端可以直接使用id=krs-server-render-data-BOX 进行数据获取。

第一个方法简单粗暴，可直接在组件内的constructor 构造函数 内进行获取，如果怕代码重复，可以写一个高阶组件。

第二个方法可以通过 context 传递，只需要在入口处传入，在组件中声明 static contextType 即可。

我是采用context 传递，为了后面方便集成 redux 状态管理 。

```

// 定义 context 生产者 组件

import React,{createContext} from 'react';
import RootContext from './route-context';

export default class Index extends React.Component {
    constructor(props,context) {
        super(props);
    }

    render() {
        return <RootContext.Provider value={this.props.initialData||{}}>
            {this.props.children}
        </RootContext.Provider>
    }
}

//入口  app.js
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import Routes from '../';
import Provider from './provider';

//渲染入口  接收脱水数据
function renderUI(initialData) {
    ReactDOM.hydrate(<BrowserRouter><Provider initialData={initialData}>
        <Routes />
    </Provider>
    </BrowserRouter>, document.getElementById('rootEle'), (e) => {
    });
}

//函数执行入口
function entryIndex() {
    let APP_INIT_DATA = {};
    let state = true;

    //取得数据
    let stateText = document.getElementById('krs-server-render-data-BOX');

    if (stateText) {
        APP_INIT_DATA = JSON.parse(stateText.value || '{}');
    }

    if (APP_INIT_DATA) {//客户端渲染

        renderUI(APP_INIT_DATA);
    }
}

//入口执行
entryIndex();

```

行文至此，核心的内容已经基本说完，剩下的就是组件内如何使用脱水的数据。

下面通过 context 拿到数据 , 代码仅供参考，可根据自己的需求来进行封装和调整。

```
import React from 'react';
import './css/index.scss';

export default class Index extends React.Component {

    constructor(props, context) {
        super(props, context);

        //将context 存储到 state 
        this.state = {
            ... context
        }

    }

    //设置此参数 才能拿到 context 数据
    static contextType = RootContext;

    //数据预取方法
    static async getInitialProps(krsOpt) {

        if (__SERVER__) {
            //如果是服务端渲染的话  可以做的处理,node 端设置的全局变量
        }

        const fetch1 = fetch.postForm('/fe_api/filed-manager/get-detail-of-type', {
            data: { ofTypeId: 4000 }
        });

        const fecth2 = fetch.postForm('/fe_api/filed-manager/get-detail-of-type', {
            data: { ofTypeId: 2000 }
        });

        const resArr = await fetch.multipleFetch(fetch1, fecth2);
        //返回所有数据
        return {
            page: {},
            fetchData: resArr
        }
    }

    componentDidMount() {
        if (!this.isSSR) { //非服务端渲染需要自身进行数据获取
            Index.getInitialProps(this.props.krsOpt).then(data => {
                this.setState({
                    ...data
                }, () => {
                   //可有的一些操作
                });
            });
        }
    }

    render() {

        //得到 state 内的数据，进行逻辑判断和容错，然后渲染
        const { page, fetchData } = this.state;
        const [res] = fetchData || [];

        return <div className="detailBox">
            {
                res && res.data.map(item => {
                    return <div key={item.id}>{item.keyId}:{item.keyName}---{item.setContent}</div>
                })
            }
        </div>
    }
}

```

到此我们的第三个问题：【服务端 html 节点无法重用 】已经解决,但人不够完美，请继续看。

# css 过滤

我们在写组件的时候大部分都会导入相关的 css 文件。

```

import './css/index.scss';//导入css

//组件
class Index extends React.Component{
    constructor(props){
        super(props);
    }

    static async  getInitialProps() {
        const fetch1 =await fetch('/xxx.com/a');
        const fetch2 = await fetch('/xxx.com/b');

        return {
            res:[fetch1,fetch2]
        }
    }

    render(){
        return <h1>{this.props.data.title}</h1>
    }
}
```

但是这个 css 文件在服务端无法执行，其实想想在服务端本来就不需要渲染 css 。为什么不直接干掉？ 所以为了方便，我这里写了一个babel 插件，在编译的时候干掉 css 的导入代码。

```

/**
 * 删除 css 的引入
 * 可能社区已经有现成的插件但是不想费劲儿找了，还是自己写一个吧。 
 */
module.exports = function ({ types: babelTypes }) {
    return {
        name: "no-require-css",
        visitor: {
            ImportDeclaration(path, state) {
                let importFile = path.node.source.value;
                if(importFile.indexOf('.scss')>-1){
                    // 干掉css 导入
                    path.remove();
                }
            }
        }
    };
};

//.babelrc 中使用

 "plugins": [
                "./webpack/babel/plugin/no-require-css"  //引入        
            ]
```

[资源搜索网站大全](https://www.renrenfan.com.cn/) https://www.renrenfan.com.cn [广州VI设计公司](https://www.houdianzi.com/)https://www.houdianzi.com

# 动态路由的 SSR

现在要说一个更加核心的内容，也是本文的一个压轴亮点，可以说是**全网唯一**,我之前也看过很多文章和资料都没有细说这一块儿的实现。

不知道你有没有发现，上面我们已经一步一步的实现了 React SSR 同构 的完整流程，但是总感觉少点什么东西。

SPA模式下大部分都会实现组件分包和按需加载，防止所有代码打包在一个文件过大影响页面的加载和渲染，影响用户体验。

那么基于 SSR 的组件按需加载如何实现呢？

当然我们所限定按需的粒度是路由级别的，请求不同的路由动态加载对应的组件。

## 如何实现组件的按需加载？

在 webpack2 时期主要使用require.ensure方法来实现按需加载，他会单独打包指定的文件，在当下 webpack4，有了更加规范的的方式实现按需加载，那就是动态导入 import('./xx.js'),当然实现的效果和 require.ensure是相同的。

咱们这里只说如何借助这个规范实现按需加载的路由，关于动态导入的实现原理先按下不表。

我们都知道 import 方法传入一个js文件地址，返回值是一个 promise 对象，然后在 then 方法内回调得到按需的组件。他的原理其实就是通过 jsonp 的方式，动态请求脚本，然后在回调内得到组件。

```
import('../index').then(res=>{
    //xxxx
});
```

那现在我们已经得到了几个比较有用的信息。

*   如何加载脚本 - import 结合 webpack 自动完成
*   脚本是否加载完成 - 通过在 then 方法回调进行处理
*   获取异步按组件 - 通过在 then 方法回调内获取

我们可以试着把上面的逻辑抽象成为一个组件，然后在路由配置的地方进行导入后，那么是不是就完成了组件的按需加载呢？

先看下按需加载组件, 目的是在 import 完成的时候得到按需的组件，然后更改容器组件的 state，将这个异步组件进行渲染。

```

/**
 * 按需加载的容器组件
 * @class Bundle
 * @extends {Component}
 */
export default class Async extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            COMPT: null
        };
    }

    UNSAFE_componentWillMount() {
        //执行组件加载
        if (!this.state.COMPT) {
            this.load(this.props);
        }
    }

    load(props) {
        this.setState({
            COMPT: null
        });
        //注意这里，返回Promise对象; C.default 指向按需组件
        props.load().then((C) => {
            this.setState({
                COMPT: C.default ? C.default : COMPT
            });
        });
    }

    render() {
        return this.state.COMPT ? this.props.children(this.state.COMPT) : <span>正在加载......</span>;
    }
}

```

Async 容器组件接收一个 props 传过来的 load 方法，返回值是 Promise类型，用来动态导入组件。

在生命周期 UNSAFE_componentWillMount 得到按需的组件，并将组件存储到 state.COMPT内,同时在 render 方法中判断这个状态的可用性，然后调用this.props.children 方法进行渲染。

```
//调用
const LazyPageCom = (props) => (
    <Async load={() => import('../index')}>
        {(C) => <C {...props} />}//返回函数组件
    </Async>
);

```

当然这只是其中一种方法，也有很多是通过 react-loadable 库来进行实现，但是实现思路基本相同,有兴趣的可以看下源码。

```
//参考代码
import React from 'react';
import Loadable from 'react-loadable';

//loading 组件
const Loading =()=>{
    return (
        <div>loading</div>
    ) 
}

//导出组件
export default Loadable({
    loader:import('../index'),
    loading:Loading
});

```

到这里我们已经实现了组件的按需加载，剩下就是配置到路由。

看下伪代码

```

//index.js

class Index extends React.Component {

    render() {
        return <div>detail</div>
    }
}

//detail.js

class Detail extends React.Component {

    render() {
        return <div>detail</div>
    }
}

//routes.js

//按需加载 index 组件
const AyncIndex = (props) => (
    <Async load={() => import('../index')}>
        {(C) => <C {...props} />}
    </Async>
);

//按需加载 detai 组件
const AyncDetail = (props) => (
    <Async load={() => import('../index')}>
        {(C) => <C {...props} />}
    </Async>
);

const routes = [

    {
        path: "/",
        exact: true,
        component: AyncIndex
    },
    {
        path: '/detail', exact: true,
        component: AyncDetail,
    }
];

```

结合路由的按需加载已经配置完成，先不管 server端 是否需要进行调整，此时的代码是可以运行的，按需也是 ok 的。

但是ssr无效了，查看网页源代码无内容。

## 动态路由 SSR 双端配置

ssr无效了,这是什么原因呢？

上面我们在做路由同构的时候，双端使用的是同一个 route配置文件routes-config.js,现在组件改成了按需加载，所以在路由查找后得到的组件发生改变了 - AyncDetail,AyncIndex，根本无法转换出组件内容。

### ssr 模式下 server 端如何处理路由按需加载

其实很简单，也是参考客户端的处理方式，对路由配置进行二次处理。server 端在进行组件查找前，强制执行 import 方法，得到一个全新的静态路由表，再去进行组件的查找。

```

//获得静态路由

import routes from 'routes-config.js';//得到动态路由的配置

export async function getStaticRoutes() {

    const staticRoutes = [];//存放新路由

    for (; i < len; i++) {
        let item = routes[i];

        //存放静态路由
        staticRoutes.push({
            ...item,
            ...{
                component: (await item.component().props.load()).default
            }
        });

    }
    return staticRoutes; //返回静态路由
}
```

如今我们离目标更近了一步，server端已兼容了按需路由的查找。但是还没完！

我们这个时候访问页面的话，ssr 生效了，查看网页源代码可以看到对应的 html 内容。

但是页面上会显示直出的内容，然后显示<span>正在加载......</span> ,瞬间又变成直出的内容。

### ssr 模式下 client 端如何处理路由按需加载

这个是为什么呢？

是不是看的有点累了，再坚持一下就成功了。

其实有问题才是最好的学习方式，问题解决了，路就通了。

首先我们知道浏览器端会对已有的节点进行双端对比，如果对比失败就会重新渲染，这很明显就是个问题。

咱分析一下，首先服务端直出了 html 内容，而此时浏览器端js执行完后需要做按需加载，在按需加载前的组件默认的内容就是<span>正在加载......</span> 这个缺省内容和服务端直出的 html 内容完全不同，所以对比失败，页面会渲染成 <span>正在加载......</span>,然后按需加载完成后组件再次渲染，此时渲染的就是真正的组件了。

**如何解决呢？**

其实也并不复杂，只是不确定是否可行，试过就知道。

既然客户端需要处理按需，那么我们等这个按需组件加载完后再进行渲染是不是就可以了呢？

答案是：可以的！

**如何按需呢？**

向“服务端同学”学习，找到对应的组件并强制 执行import按需，只是这里不是转换为静态路由，只找到按需的组件完成动态加载即可。

既然有了思路，那就撸起代码。

```

import React,{createContext} from 'react';
import RootContext from './route-context';

export default class Index extends React.Component {
    constructor(props,context) {
        super(props);
    }

    render() {
        return <RootContext.Provider value={this.props.initialData||{}}>
            {this.props.children}
        </RootContext.Provider>
    }
}

//入口  app.js
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import Routes from '../';
import Provider from './provider';

//渲染入口
function renderUI(initialData) {
    ReactDOM.hydrate(<BrowserRouter><Provider initialData={initialData}>
        <Routes />
    </Provider>
    </BrowserRouter>, document.getElementById('rootEle'), (e) => {
    });
}

function entryIndex() {
    let APP_INIT_DATA = {};
    let state = true;

    //取得数据
    let stateText = document.getElementById('krs-server-render-data-BOX');

    //数据脱水
    if (stateText) {
        APP_INIT_DATA = JSON.parse(stateText.value || '{}');
    }

    if (APP_INIT_DATA) {//客户端渲染

        - renderUI(true, APP_INIT_DATA);
        //查找组件
        + matchComponent(document.location.pathname, routesConfig()).then(res => {
            renderUI(true, APP_INIT_DATA);
        });
    }
}

//执行入口
entryIndex();

```

matchComponent 是我封装的一个组件查找的方法，在文章开始已经介绍过类似的实现，代码就不贴了。

**核心亮点说完，整个流程基本结束，剩下的都是些有的没的了，我打算要收工了。**

# 其他

## SEO 支持

页面的 SEO 效果取决于页面的主体内容和页面的 TDK（标题 title,描述 description,关键词 keyword）以及关键词的分布和密度，现在我们实现了 ssr所以页面的主体内容有了，那如何设置页面的标题并且让每个页面（路由）的标题都不同呢？

只要我们每请求一个路由的时候返回不同的 tdk 就可以了。

这里我在所对应组件数据预取的方法内加了约定，返回的数据为固定格式，必须包含 page 对象，page 对象内包含 tdk 的信息。

看代码瞬间就明白。

```

import './css/index.scss';

//组件
class Index extends React.Component{
    constructor(props){
        super(props);
    }

    static async  getInitialProps() {
        const fetch1 =await fetch('/xxx.com/a');
        const fetch2 = await fetch('/xxx.com/b');

        return {
            page:{
                tdk:{
                    title:'标题',
                    keyword:'关键词',
                    description:'描述'
                }
            }
            res:[fetch1,fetch2]
        }
    }

    render(){
        return <h1>{this.props.data.title}</h1>
    }
}
```

这样你的 tdk 可以根据你的需要设置成静态还是从接口拿到的。然后可以在 esj 模板里进行绑定，也可以在 componentDidMount通过 js
document.title=this.state.page.tdk.title设置页面的标题。

```
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <meta http-equiv="X-UA-Compatible" content="ie=edge">
   <meta name="keywords" content="<%=page.tdk.keyword%>" />
   <meta name="description" content="content="<%=page.tdk.description%>" />
   <title><%=page.tdk.title%></title>
</head>
<body>
   <div id="rootEle">
       <%- htmlContent %>
   </div>
   <%- propsData %>
</body>
</html>
</body>
<%page.staticSource.js.forEach(function(item){%>

```

## fetch 同构

可以使用isomorphic-fetch、axios或者whatwg-fetch + node-fetch 等库来实现支持双端的 fetch 数据请求，这里推荐使用axios 主要是比较方便。

# TODO 和 思考

没有介绍结合 redux 状态管理的 ssr 实现，其实也不复杂，关键还是看业务中是否需要使用redux，因为文中已经实现了使用 context 传递数据，直接改成按store 传递也很容易,但是更多的还是对 react-redux 的应用。

```

//渲染入口 代码仅供参考 
function renderUI(initialData) {
   ReactDOM.hydrate(<BrowserRouter><Provider store={initialData}>
       <Routes />
   </Provider>
   </BrowserRouter>, document.getElementById('rootEle'), (e) => {
   });
}

```

服务端同构渲染虽然可以提升首屏的出现时间，利于 SEO，对低端用户友好，但是开发复杂度有所提高，代码需要兼容双端运行（runtime）,还有一些库只能在浏览器端运行，在服务端加载会直接报错，这种情况就需要进行做一些特殊处理。

同时也会大大的增加服务端负载，当然这都容易解决，可以改用renderToNodeStream() 方法通过流式输出来提升服务端渲染性能，可以进行监控和扩容，所以是否需要 ssr 模式，还要看具体的产品线和用户定位。
