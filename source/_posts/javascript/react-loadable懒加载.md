---
title: react-loadable懒加载
date: 2021-03-11 21:12:40
categories: 前端
---

#### 简介

**react-loadable**
官网最精简的描述：

> A higher order component for loading components with dynamic imports.
> 用于加载带有动态导入的组件的高阶组件。

React Loadable是一个小型库，它使以组件为中心的代码分割变得非常容易。

#### 背景

当你的React应用，你把它和Webpack捆绑在一起，一切都很顺利。但有一天你会注意到你的应用程序包变得如此之大以至于它会减慢速度。
是时候开始分解你的应用程序代码了!

例如以下情景：

```
class Root extends React.Component {
    render() {
        return (
            <Router>
                <div>
                    <Route exact path="/" component={PCIndex}></Route>
                    <Route path="/details/:id" component={PCDetails}></Route>
                </div>
            </Router>
        );
    }
}

ReactDOM.render(
    <Root />,
    document.getElementById('app'));
```

**这种写法缺点：**
在首页直接加载了组件，如果很多的时候，那么你的应用首屏展现的时候就会变得非常慢了。

**react-loadable的作用**
由于路由只是组件，我们仍然可以在路由级别轻松地进行代码拆分。
在你的应用程序中引入新的代码分割点应该是如此简单，以至于你不会再三考虑。这应该是一个改变几行代码和其他一切都应该自动化的问题。
Loadable是一个高阶组件(一个创建组件的函数)，它允许您在将任何模块呈现到应用程序之前动态加载它。

#### 概念

**import()**
当您在Webpack 2+中使用import()时，它将自动为您分割代码，而不需要额外的配置。
这意味着只需切换到import()并使用React Loadable，就可以轻松地试验新的代码分割点。找出最适合你的应用程序的。

**Loading...**
呈现静态“Loading...”不能与用户进行足够的通信。您还需要考虑错误状态、超时，并使之成为一种良好的体验。

**Loadable**
用于在呈现模块之前动态加载模块的高阶组件，在模块不可用时呈现加载组件。

```
const LoadableComponent = Loadable({
  loader: () => import('./Bar'),
  loading: Loading,
  delay: 200,
  timeout: 10000,
});
```

**Loadable.Map**
允许并行加载多个资源的高阶组件。
可加载的。地图的选择。加载器接受函数对象，并需要一个选项。渲染方法。

```
Loadable.Map({
  loader: {
    Bar: () => import('./Bar'),
    i18n: () => fetch('./i18n/bar.json').then(res => res.json()),
  },
  render(loaded, props) {
    let Bar = loaded.Bar.default;
    let i18n = loaded.i18n;
    return <Bar {...props} i18n={i18n}/>;
  }
});
```

#### 简单使用

安装

```
npm i react-loadable
```

**懒加载配置/router/index.js**

```
import React from 'react'
import Loadable from "react-loadable"

let config = [
    {
        name: '/',
        path: '/',
        exact: true,
        component: Loadable({
            loader: () => import('../components/pc/pc_blog_content'),
            loading: () => <div />
        })
    },
    {
        name: 'home',
        path: '/home',
        exact: true,
        component: Loadable({
            loader: () => import('../components//pc/pc_blog_content'),
            loading: () => <div />
        })
    },
    {
        name: 'detail',
        path: '/detail/:id',
        exact: false,
        component: Loadable({
            loader: () => import('../components/pc/pc_article_detail'),
            loading: () => <div />
        })
    },
    {
        name: 'timeline',
        path: '/timeline',
        exact: true,
        component: Loadable({
            loader: () => import('../components/pc/pc_timeline_index'),
            loading: () => <div />
        })
    },
    {
        name: 'album',
        path: '/album',
        exact: true,
        component: Loadable({
            loader: () => import('../components/pc/pc_album_index'),
            loading: () => <div />
        })
    },
    {
        name: 'message',
        path: '/message',
        exact: true,
        component: Loadable({
            loader: () => import('../components/pc/pc_message_index'),
            loading: () => <div />
        })
    },
    {
        name: 'about',
        path: '/about',
        exact: true,
        component: Loadable({
            loader: () => import('../components/pc/pc_aboutme_index'),
            loading: () => <div />
        })
    },
    {
        name: 'albumlList',
        path: '/albumList/:id',
        exact: false,
        component: Loadable({
            loader: () => import('../components/pc/pc_album_list'),
            loading: () => <div />
        })
    }

]

export default config
```

结合react-router4
官网参考
[https://reacttraining.com/rea...](https://reacttraining.com/react-router/web/guides/code-splitting)

```
import React from 'react';
import ReactDOM from 'react-dom';
import PCBlogIndex from './components/pc/pc_blog_index'
//这句就是引入react-loadable
import routers from './router/index';
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
import { Provider } from 'react-redux'
import configureStore from './store/configureStore'
const store = configureStore();

class Root extends React.Component {

    render() {
        return (
            <Provider store={store}>
            <Router>
                <Switch>
                    <PCBlogIndex>
                             {/* 导入相关路由配置 */}
                        {routers.map((r, key) => <Route component={r.component} exact={!!r.exact} key={key} path={r.path} />)}
                    </PCBlogIndex>
                </Switch>
            </Router>
            </Provider>
        );
    }
}

ReactDOM.render(
    <Root />,
    document.getElementById('app'));
```

#### 总结

关于react-loadable服务器渲染等更加高级操作可以参考官网
[https://github.com/jamiebuild...](https://github.com/jamiebuilds/react-loadable)
至于普通的操作按上面两步操作，结合官网的相关配置API，估计你能够实现懒加载的功能了。
