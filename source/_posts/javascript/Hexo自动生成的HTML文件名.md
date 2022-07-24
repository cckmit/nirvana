---
title: Hexo自动生成的HTML文件名
date: 2022-03-26 16:28:03
categories: 前端
tags: [hexo]
---
我们在使用Hexo框架生成静态博客时，其实是将你写好的.md文件输出成HTML文件进行渲染，其中HTML的文件名称就是.md的文件名称。
通常是把.md文件命名成中文的甚至是文章的标题，那么生成HTML文件时也就是中文的文件名了。
这样就会带来一个比较麻烦的问题就是，url的链接过长，不利于记录和同步，而且对搜索引擎不友好。例如`什么样的问题应该使用动态规划`这段中文url就编译成了`%E4%BB%80%E4%B9%88%E6%A0%B7%E7%9A%84%E9%97%AE%E9%A2%98%E5%BA%94%E8%AF%A5%E4%BD%BF%E7%94%A8%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92/`
我们可以在Hexo生成HTML文件时，修改HTML的命名策略，即将原始的命名方式改为我们自定义的命名方式？
## 修改配置文件
Hexo的根配置文件_config.yml，打开它，修改配置文件
```
permalink: pages/:abbrlink.html ##abbrlink就是我们动态生产的文件名

```
我把他改成这样了，page是目录，执行`hexo g`会在public下生成，我让生成的HTML文件都放在page下，`:fileName.html`是HTML的命名格式，其中`fileName`是个变量。这个变量从哪来？
## 文件名动态生成
为保证文件名在文件的名字不变，我们把文件名和`Title`关联起来，然后通过`base64`进行编码，这样只要title不变我们文件也不变，同时这个改变最好在进行文档生成就执行，所以我们需要把该功能做成插件[hexo-abbrlink-base64](https://www.npmjs.com/package/hexo-abbrlink-base64)。
源码如下：
```
const eng = ['a','b','c','d','e','f','g','h','i','j'];

//文件名Base64后 根据算法从编码中取10位作为新文件名
function getNewNameBase64(fileName){
    var fi_bs64 = Buffer.from(fileName).toString('base64')//转成base64编码
    var arr = fi_bs64.split("");
    var len = arr.length;
    var fis = "";
    for(var i=1;i<=10;i++){
        var s1 = arr[Math.floor(len/(2*i))];
        if(s1=='/'||s1=='+'){
            var eng_index = Math.floor(Math.random()*10+1);
            s1 = eng[eng_index-1];
        }
        fis = fis + s1;
    }
    return fis;
}
/**
 * fitler hexo's post, auto generate `abbrlink`
 *
 * @param log
 * @param data
 */
function filterPost(log, data) {
    if (!data.abbrlink) {
        data.abbrlink = getNewNameBase64(data.title);
        log.i("Generate abbrlink [%s] for post [%s]", data.abbrlink, data.source);
    }
}
hexo.extend.filter.register('before_post_render', function (data) {
    if (data.layout === 'post') {
        filterPost(this.log, data);
    }
    return data;
});
```

生产的文件格式如下：
```
INFO  Generated: pages/KP6qyyyLLL.html
INFO  Generated: pages/eejFFWWWWW.html
INFO  Generated: pages/DWujXduull.html
INFO  Generated: pages/sphaII00ll.html
INFO  Generated: pages/525Cmmbbbb.html
INFO  Generated: pages/5q5FaaYYYY.html
INFO  Generated: pages/pYjduulllG.html
INFO  Generated: pages/iXjnbpdddd.html
INFO  Generated: pages/5S50ll2222.html
```
使用abbrlink之后，文章的url就会变得非常简洁，例如 https://XXXXXX/pages/5S50ll2222.html
有一些插件会根据标题的拼音、翻译生成permlink，但是我感觉这些都不是好的做法。事实上url不宜太长，将一句话放在url中并不一定增强所谓的seo优化，反倒导致其使用起来相当不便。
就像许多博客平台一样，为每篇文章分配短小且唯一的url即可，无论是看起来还是用起来都很方便。

