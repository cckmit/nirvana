---
title: JavaScript判断浏览器类型及版本
date: 2021-03-11 21:12:40
categories: 前端
---

浏览器对于我们来说，可能是最熟悉的工具了。记得最早那会Netscape，到后来的Internet Explorer一统江湖，再到现在的FireFox大行其道，浏览器市场的争夺，可谓是硝烟弥漫。熟知的浏览器Firefox、Opera、Safari、IE、Chrome以外，据说世界上还有近百种浏览器。

在我们的产品开发过程中，经常要考虑浏览器兼容问题，以适应不同的用户人群，同时也减少因浏览器兼容问题所带来的服务量。一般来说，通过JavaScript判断浏览器类型，我们通常使用两种方法，一种是根据各种浏览器独有的属性来分辨，另一种是通过分析浏览器的user-Agent属性来判断的。在许多情况下，值判断出浏览器类型之后，还需判断浏览器版本才能处理兼容性问题，而判断浏览器的版本一般只能通过分析浏览器的User-Agent才能知道。现在比较流行的javascript框架，都有浏览器兼容的判断代码，像jquery、YUI就是使用User-Agent，而Mootools则是使用用各种浏览器独有的属性来分辨。

先来看看什么是`User-Agent`？User-Agent是HTTP请求中的用户标识，一般发送一个能够代表客户端类型的字符串，比如浏览器类型 操作系统等信息。User-Agent 的约定格式是：应用名，跟一个斜线，跟版本号，剩下的是自由的格式。

### 我们先来看一下常用浏览器的User-Agent：
`IE`
```
      Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0)
      Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.2)
      Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
      Mozilla/4.0 (compatible; MSIE 5.0; Windows NT)
      其中，版本号是MSIE之后的数字。
```
`Firefox`
``` 
Mozilla/5.0 (Windows; U; Windows NT 5.2) Gecko/2008070208 Firefox/3.0.1
Mozilla/5.0 (Windows; U; Windows NT 5.1) Gecko/20070309 Firefox/2.0.0.3
Mozilla/5.0 (Windows; U; Windows NT 5.1) Gecko/20070803 Firefox/1.5.0.12
其中，版本号是Firefox之后的数字。
```
·Safari·
```
Mozilla/5.0 (Windows; U; Windows NT 5.2) AppleWebKit/525.13 (KHTML, ** Gecko) Version/3.1 Safari/525.13
Mozilla/5.0 (iPhone; U; CPU ** Mac OS X) AppleWebKit/420.1 (KHTML, ** Gecko) Version/3.0 Mobile/4A93 Safari/419.3 
其版本号是Version之后的数字。
```
`Chrome`
```
Mozilla/5.0 (Windows; U; Windows NT 5.2) AppleWebKit/525.13 (KHTML, ** Gecko) Chrome/0.2.149.27 Safari/525.13
其中，版本号在Chrome只后的数字。
```
## 一.基于User-Agent信息的实现
```
 var Sys = {};  
var ua = navigator.userAgent.toLowerCase();  
var s;  
(s = ua.match(/msie ([\d.]+)/)) ? Sys.ie = s[1] :  
(s = ua.match(/firefox\/([\d.]+)/)) ? Sys.firefox = s[1] :  
(s = ua.match(/chrome\/([\d.]+)/)) ? Sys.chrome = s[1] :  
(s = ua.match(/opera.([\d.]+)/)) ? Sys.opera = s[1] :  
(s = ua.match(/version\/([\d.]+).*safari/)) ? Sys.safari = s[1] : 0;   

 //以下进行测试  
if (Sys.ie) document.write('IE: ' + Sys.ie);  
if (Sys.firefox) document.write('Firefox: ' + Sys.firefox);  
if (Sys.chrome) document.write('Chrome: ' + Sys.chrome);  
if (Sys.opera) document.write('Opera: ' + Sys.opera);  
if (Sys.safari) document.write('Safari: ' + Sys.safari);  
```
##  二.根据浏览器特有属性判断
```
var Browser = {

    Engine: {name: 'unknown', version: 0},

    Features: {
        xpath: !!(document.evaluate),     // 是否支持 XPath
        air: !!(window.runtime),        // 是否支持 Air 扩展
        query: !!(document.querySelector) // 是否支持 CSS 选择器
    },

    Engines: {
        // 判断 Opera
        presto: function () {
            return (!window.opera) ? false : ((arguments.callee.caller) ? 960 : ((document.getElementsByClassName) ? 950 : 925));
        },
        // 判断 IE，根据 ActiveX 和 特有的 XMLHttpRequest 对象
        trident: function () {
            return (!window.ActiveXObject) ? false : ((window.XMLHttpRequest) ? 5 : 4);
        },
        // Webkit 核心的浏览器，如 Safari 和 Chrome
        webkit: function () {
            return (navigator.taintEnabled) ? false : ((Browser.Features.xpath) ? ((Browser.Features.query) ? 525 : 420) : 419);
        },
        // Mozilla Gecko 核心浏览器，如 Firefox
        gecko: function () {
            return (document.getBoxObjectFor == undefined) ? false : ((document.getElementsByClassName) ? 19 : 18);
        }
    }
};
或者
var Sys = {};
var ua = navigator.userAgent.toLowerCase();
window.ActiveXObject ? Sys.ie = ua.match(/msie ([\d.]+)/)[1] :
    document.getBoxObjectFor ? Sys.firefox = ua.match(/firefox\/([\d.]+)/)[1] :
        window.MessageEvent && !document.getBoxObjectFor ? Sys.chrome = ua.match(/chrome\/([\d.]+)/)[1] :
            window.opera ? Sys.opera = ua.match(/opera.([\d.]+)/)[1] :
                window.openDatabase ? Sys.safari = ua.match(/version\/([\d.]+)/)[1] : 0;

//以下进行测试
if (Sys.ie) document.write('IE: ' + Sys.ie);
if (Sys.firefox) document.write('Firefox: ' + Sys.firefox);
if (Sys.chrome) document.write('Chrome: ' + Sys.chrome);
if (Sys.opera) document.write('Opera: ' + Sys.opera);
if (Sys.safari) document.write('Safari: ' + Sys.safari);
```
但是，这两种检测方法都有其局限性，User-Agent 是可以通过代码修改的，所以纯粹检测User-Agent可能会带来一定的风险，而且ie8的Compatibility View 功能造成单浏览器会“原生”多个 User-agent 。使用不同特征来判断浏览器的方法，

虽然在速度上比用正则表达式分析userAgent要来的快，不过这些特征可能会随浏览器版本而变化。比如，一种浏览器本来独有的特性取得了市场上的成功，其他浏览器也就可能跟着加入该特性，从而使该浏览器的独有特征消失，导致我们的判断失败。因此，

相对比较保险的做法是通过解析userAgent中的特征来判断浏览器类型。何况，反正判断版本信息也需要解析浏览器的userAgent的。所以，在我们的实际编程中，还得根据实际情况来决定使用哪种方法来判断浏览器类型和版本。

ppk on javascript中有关浏览器检测的看法和建议

1、Opera，iCab等非主流浏览器提供给用户自己修改识别字符串，以访问更多的网站。
2、Opera 在navigator.userAgent永远包含一个子字符串Opera，即使当它处于伪装模式时也一样。而且Opera是唯一支持window.opera属性的浏览器
3、Safari、iCab和Konqueror 在navigator.userAgent有它们自己的标识符，那他们也有可能不包含，为了伪装。
4、Gecko  Mozilla的识别字符串中通常包含Gecko，不过，Safari的默认字符串中也包含Gecko
5、MSIE 大部分浏览器都拿它来伪装，因此它的出现说明不了任何问题。
## 三.ppk中有关浏览器检测的代码
```
var BrowserDetect = {
    init: function () {
        this.browser = this.searchString(this.dataBrowser) || "An unknown browser";
        this.version = this.searchVersion(navigator.userAgent)
            || this.searchVersion(navigator.appVersion)
            || "an unknown version";
        this.OS = this.searchString(this.dataOS) || "an unknown OS";
    },
    searchString: function (data) {
        for (var i=0;i<data.length;i++)    {
            var dataString = data[i].string;
            var dataProp = data[i].prop;
            this.versionSearchString = data[i].versionSearch || data[i].identity;
            if (dataString) {
                if (dataString.indexOf(data[i].subString) != -1)
                    return data[i].identity;
            }
            else if (dataProp)
                return data[i].identity;
        }
    },
    searchVersion: function (dataString) {
        var index = dataString.indexOf(this.versionSearchString);
        if (index == -1) return;
        return parseFloat(dataString.substring(index+this.versionSearchString.length+1));
    },
    dataBrowser: [
        {
            string: navigator.userAgent,
            subString: "Chrome",
            identity: "Chrome"
        },
        {     string: navigator.userAgent,
            subString: "OmniWeb",
            versionSearch: "OmniWeb/",
            identity: "OmniWeb"
        },
        {
            string: navigator.vendor,
            subString: "Apple",
            identity: "Safari",
            versionSearch: "Version"
        },
        {
            prop: window.opera,
            identity: "Opera"
        },
        {
            string: navigator.vendor,
            subString: "iCab",
            identity: "iCab"
        },
        {
            string: navigator.vendor,
            subString: "KDE",
            identity: "Konqueror"
        },
        {
            string: navigator.userAgent,
            subString: "Firefox",
            identity: "Firefox"
        },
        {
            string: navigator.vendor,
            subString: "Camino",
            identity: "Camino"
        },
        {        // for newer Netscapes (6+)
            string: navigator.userAgent,
            subString: "Netscape",
            identity: "Netscape"
        },
        {
            string: navigator.userAgent,
            subString: "MSIE",
            identity: "Explorer",
            versionSearch: "MSIE"
        },
        {
            string: navigator.userAgent,
            subString: "Gecko",
            identity: "Mozilla",
            versionSearch: "rv"
        },
        {         // for older Netscapes (4-)
            string: navigator.userAgent,
            subString: "Mozilla",
            identity: "Netscape",
            versionSearch: "Mozilla"
        }
    ],
    dataOS : [
        {
            string: navigator.platform,
            subString: "Win",
            identity: "Windows"
        },
        {
            string: navigator.platform,
            subString: "Mac",
            identity: "Mac"
        },
        {
               string: navigator.userAgent,
               subString: "iPhone",
               identity: "iPhone/iPod"
        },
        {
            string: navigator.platform,
            subString: "Linux",
            identity: "Linux"
        }
    ]
 
};
BrowserDetect.init();
document.write('你正在使用的是：' + BrowserDetect.browser + ' ' + BrowserDetect.version + ' on ' + BrowserDetect.OS + '!');
```
