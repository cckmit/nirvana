---
title: HTTP首部有哪些字段？
date: 2022-03-09 16:28:03
categories: 前端
---
## 一、HTTP请求/响应报文介绍
![](https://upload-images.jianshu.io/upload_images/10024246-c1a0b63ff825954b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一个完整的 HTTP消息格式分三部分：

- 请求行: {请求方法} {资源路径} {协议版本}
- 请求头: 紧跟请求行的下一行，所有的请求头，除Host外都是可选的。
- 空行: 告诉服务器请求头部到此为止。
- 消息体: 消息的主体部分，消息体的数据格式通过 header 里面的 Content-Type 属性指定。

报文首部被分为：请求行（或者状态行）和请求头和和其他（如Cookie），请求头又被分为请求首部字段、通用首部字段、实体首部字段和其他
请求报文和响应报文的报文首部由以下数据构成。
|请求行/状态行|首部字段|其他|
|--|--|--|
|请求行包含用于请求的方法，请求URI和HTTP版本状态行包含表明响应结果的状态码，原因短语和HTTP版本|包含表示请求或响应的各种条件和属性的各类首部，一般包括4种首部：通用首部、请求首部、响应首部和实体首部|可能包含HTTP的RFC里未定义的首部（Cookie）|

报文主体通常等于实体主体，只有当传输中进行编码操作时，实体主体的内容发生变化，才导致它和报文主体产出差异。（实体主体被gzip等编码之后，再放到HTTP报文主体中，报文主体就不等于实体主体）
## 二、首部字段一览表

>HTTP/1.1中定义了47种首部字段
还有一些Cookie、Set-Cookie和Content-Disposition等在其他RFC中定义的首部字段，使用频率也很高。

##### 通用首部字段： 9种
|首部字段名|说明|
|--|--|
|Cache-Control|控制缓存行为|
|Connection|逐跳首部、连接的管理|
|Date|创建报文的日期时间|
|Pragma|报文指令|
|Trailer|报文末端的首部一览|
|Transfer-Encoding|指定报文传输主体的编码方式|
|Upgrade|升级为其他协议|
|Via|代理服务器的相关信息|
|Warning|错误通知|
##### 请求首部字段： 19种
|首部字段名|说明|
|--|--|
|Accept用户代理可以处理的媒体类型|
|Accept-Charset|优先的字符集|
|Accept-Encoding|优先的内容编码|
|Accept-Language|优先的语言|
|AuthorizationWeb|认证信息|
|Except|期待服务器的特定行为|
|From|用户的电子邮箱地址|
|Host|请求资源所在的服务器|
|If-Match|比较实体标记（ETag）|
|If-Modified-Since|比较资源的更新时间|
|If-None-Match|比较实体标记|
|If-Range|资源未更新时发送实体Byte的范围请求|
|If-Unmodified-Since|比较资源的更新时间|
|Max-Forwards|最大传输逐跳数|
|Proxy-Authorization|代理服务器要求客户端的认证信息|
|Range|实体的字节范围请求|
|Referer|对请求中URI的原始获取方TE传输编码的优先级|
|User-Agent|HTTP客户端程序的信息|
##### 响应首部字段： 9种
|首部字段名|说明|
|--|--|
|Accept-Ranges|是否接受字节范围请求|
|Age|推算资源创建经过的时间|
|ETag|资源的匹配信息|
|Location|令客户端重定向至指定UPI|
|Proxy-Authenticate|代理服务器对客户端的认证信息|
|Retry-After|对再次发起请求的时机要求|
|ServerHTTP|服务器的安装信息|
|Vary|代理服务器的管理信息|
|WWW-Authenticate|服务器对客户端的认证信息|
##### 实体首部字段： 10种
|首部字段名|说明|
|--|--|
|Allow|资源可支持的HTTP方法|
|Content-Encoding|实体主体适用的编码方式|
|Content-Language|实体主体的自然语言|
|Content-Length|实体主体的大小（单位：字节）|
|Content-Location|替代对应资源的URI|
|Content-MD5|实体主体的报文摘要|
|Content-Range|实体主体的位置范围|
|Content-Type|实体主体的媒体类型|
|EXpires|实体主体过期的日期时间|
|Last-Modified|资源的最后修改日期时间|
## 三、常用字段

##### 3.1 Content-Type
发送数据时，必须指明要发送的数据的类型，这时要使用`Content-Type`
`Content-Type`字段指明实体的类型如：`text/plain`、`application/pdf`、`text/html`等，如果有报文主体有多个类型或多个范围，则Content-Type的值要置为`mutipart/form-data`或者`multipart/byteranges`，《图解HTTP》把这个叫做“多部分对象集合”
- `Content-Type`: `mutipart/form-data`;
在Web表单文件上传的时候使用
- `Content-Type`: `multipart/byteranges`;
响应报文包含了多个范围（不是多种类型）的内容时使用。当应用`Content-Type`: `multipart/byteranges`;时，返回的状态码必然是`206 Partial Content`。

若要发送“多部分对象集合”，还要使用boundary用来分隔这些不同类型的对象

- `Content-Type: mutipart/form-data; boundary=AaB03x`
- `Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES`

在报文主体中，`boundary`字段的取值不是固定的

- `boundary`字段指定的各个实体的起始行之前插入“`--boundary`”标记，然后在多部分对象集合的最后插入“`--boundary--`”作为整体的结束标记。（这里的boundary要进行“取值操作”）
- ”多部分对象集合“的每个部分类型中，都可以含有首部字段。并且可以在某个部分中嵌套使用多部分对象集合。

##### 3.2 Range
发送请求且要获取数据时，可以使用`Range`字段指定要获取的范围
这里讲了`Range`字段的三种取值方式

- `Range: bytes=5001-1000`
- `Range: bytes=5001-`
- `Range: bytes=-300, 5000-7000`

针对范围请求，服务端在发回数据的时候会带上`Content-Type: multipart/byteranges`，且响应的状态码是`206 Partial Content`.如果服务器无法响应范围请求则返回`200 OK`
##### 3.3 Accept-？
客户端请求资源之前，可能就响应的内容进行协商，返回客户端最适合的资源
内容协商的内容包括响应资源的语言、字符集、编码方式等
首部的字段包括

- `Accept`
- `Accept-Charset`
- `Accept-Encoding`
- `Accept-Language`

顺便说一下，内容协商技术有以下3种类型


- 服务器驱动协商
根据客户端请求的首部字段为参考，在服务端自动处理。但对用户来说，以浏览器发送的首部字段作为判断的依据，不能总是满足客户的需求
- 客户端驱动协商
用户从浏览器显示的可选项列表中手动选择，或利用JavaScript脚本在Web页面上自动进行上述选择。比如按OS的类型或者浏览器的类型，自动切换成PC版页面或手机版页面
- 透明协商
是服务器端和客户端各自进行内容协商的一种办法

##### 3.4 Via
不论请求或响应，每次经过代理服务器的时候，都会在首部追加一个`Via`
如请求经过两个代理服务器`proxy1`、`proxy2`时，相应的Via为：

- `Via: proxy1`
- `Via: proxy2, proxy`

使用首部字段`Via`是为了跟踪客户端与服务端之间的请求和响应报文的传输路径。常常和`Trace`方法一起使用。
##### 3.5 Cache-Control
`Cache-Control`是通信双方都会用到的首部，用来处理缓存，`Cache-Control`有缓存请求指令和缓存响应指令之分。
请求指令一共八个，只讲下面几个

- `no-cache`：强制向源服务器再次验证（强行跳过本地缓存或代理缓存，请求服务端）
- `no-store`：让服务端不缓存请求的任何内容
- `max-age`：响应的最大Age值（如果缓存没超过`Age`值，就去本地缓存或代理缓存去拿资源）

响应指令一共10个，只讲下面几个
- `no-cache`：缓存前必须先确认其有效性（规定客户端每次使用缓存都要向服务器确认一下有效性）
- `no-store`：让客户端不缓存响应的任何内容
- `max-age`：响应的最大Age值（如果没有超过Age值，则不要来找我（服务器），直接去缓存里面找资源）



##### 3.6 Upgrade
`Upgrade`用于检测`HTTP`协议以及其他协议是否可以用更高的版本进行通信，其参数值可以用来指定一个完全不同的通信协议
如：`Upgrade :TLS/1.0`
##### 3.6 Connection
`Connection`首部字段具备如下作用
- 管理不再转发给代理的首部字段
如 `Connection: Upgrade`，则代理服务器先删除请求报文中的`Upgrade`字段再转发
- 管理持久连接
`Connection:close/keep-alive`
##### 3.7 Authorization
首部字段`Authorization`是用来告诉服务器，用户的代理认证信息（证书值）。通常，想要通过服务器认证的用户代理会在接收到返回的4.1状态码响应后，把`Authorization`加入请求中。
##### 3.8 Expect
`Expect`告诉服务器，行为服务器做出的响应行为。若服务器无法理解客户端的期望做出回应而发生错误时，会返回`417 Expectation Failed`
如：`Expect: 100-continue`
##### 3.9 If-Match
`If-xxx`这样的请求被称为条件请求。只有条件为真的时候，才会执行请求
`If-Match`对应`Etag`（实体标记）字段，当客户端发送`If-Match`的时候，就是要和服务端保存的`Etag`作对比，一样才得到`true`并返回响应。如果不一样则返回`412 Precondition Failed`的响应
##### 3.10 If-Modified-Since
`If-Modified-Since`的指令是一个日期时间，这个时间指令会和服务器端的资源最后修改时间作对比，若资源最后修改时间比这个时间指令晚，则得到true并返回响应
##### 3.11 If-None-Match/If-Unmodified-Since
与`If-Match`相反
##### 3.12 If-Range
告知服务器指定的`If-Range`字段值（Etag值或者时间）和请求资源的`Etag`值或时间相一致时，则作为范围请求处理。反之，则返回全体资源。
##### 3.13 Proxy-Authorization
这是个请求首部字段
会把由代理服务器所要求的认证信息发送给客户端。
##### 3.14 ETag
`ETag`是响应首部字段。
服务器会为每一份资源分配对应的Etag值。
`ETag`被分为·强Etag`值和·弱Etag·值。
- `强ETag`值不论实体发生多么细微的变化都会改变其值。
- `弱ETag`值只用于提示资源是否相同。只有资源发生了根本改变，产生差异时才会改变`Etag`值。`弱ETag`值只在字段值最开始处附加W/。
##### 3.15 Location
基本上该字段会配合`3xx`状态码表示重定向的地址
##### 3.16 Content-Encoding
实体主体若进行了编码压缩，则要使用`Content-Encoding`告知客户端如何解码
##### 3.17 Expires
`Expires`字段值是资源失效的日期。在`HTTP/1.1`中，`Cache-Control`的`max-age`优先于`Expires`
#####3.18 Set-Cookie
Set-Cookie各个属性如下
|属性|说明|
|--|--|
|NAME=VALUE|赋予Cookie的名称和其值|
|expires-DATE|Cookie有效期（如果不指明则默认为浏览器关闭前为止）|
|path=PATH|将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录）|
|domain=域名|作为Cookie适应对象的域名（若不指定则默认创建服务器的域名）|
|Secure|仅在HTTPS安全通信时才会发送|
|CookieHttpOnly|加以限制，使Cookie不能被JavaScript脚本访问|
##### 3.19 Cookie
`Cookie`会告知服务器，当客户端想获得HTTP状态管理支持时，就会在请求中包含从服务器接收到的`Cookie`。接收到多个`Cookie`时，同样也可以以多个`Cookie`形式发送。

