---
title: URL编码
date: 2021-03-11 21:12:40
category: javascript
---

URL的架构-------------------
<scheme>://<username>:<password>@<host>:<port>/<parameters>?<query>#<fragment>
<模式> ://<用户名>:<密码>@<主机地址>:<端口>/<路径>;<参数>?<查询>#<信息片断>

模式-------
通常是指用来访问URL所标明地址的资源的协议.
如果一个模式向IANA注册过,那么它就是官方的(如HTTP,FTP)
但也有非官方的(未注册)的模式在普遍使用,(如SFTP,svn)
scheme: 为一部分,//不属于分隔符的一部分,是URL下一部分的开始.

{ .....//<用户名>:<密码>@<主机地址>:<端口>...}---------------------------------------------------------------
构成了URL的权限控制部分

只提供用户名
ftp://some_user@code.google.com/
提供用户名和密码
ftp://some_user:password@code.google.com/
如果你不提供用户名和密码,而你试图访问的URL要求你提供,那么应用程序(如:浏览器)将提供一些默认值.

主机---------
主机地址可以是域名或IP地址,正如我们所知,域名会解析成IP地址(通过DNS查找)以确定我们正在尝试访问
的机器的地址.

端口--------
它告诉我们,我们连接到机器上的指定应用正在监听什么网络端口,如果省略,则使用默认值 ,HTTP的默认为80

路径--------
以 / 作为前缀从URL组成部分中分离.一个路径由一系列的 / 字符分隔的片断组成.
告诉我们资源在服务器上的位置,每个路径片断都可以包含参数,以 ;  号与路径片断隔开,如:
http://www.soon.com/aaa;para1=xxx/bbb;para2=yyy/my.html
这样的URL是完全有效的.

参数---------
它们可以出现在路径之后,查询字符串之前,以 ; 号与URL的其它部分和其它参数隔开,如:
http://www.soon.com/aaa/bbb/ccc/my.html;para1=xyz;para2=abc
它们不是很常见.

查询---------
它们十分常见.
这是发送一些参数到服务器资源上的首选方式.
这些以  关键字=值  的查询对以?号与URL的其它部分隔开,并且以&符号与其它的 [查询对] 分开.使用 ; 号将 [查询对] 分开也是合法的. 
>http://www.soon.com/bbs/about.html?para1=ok&para2=yes    等价于
http://www.soon.com/bbs/about.html?para1=ok;para2=yes

信息片断---------------
这是一个URL的可选部分,用于描述资源的一个特定部分.我们通常看到它们用于链接到HTML文档中的特定部分.信息片断以#号与URL的其它部分隔开.当从服务器请求一个以URL寻址的资源时,客户端通常不会发送信息片断到服务器(至少HTTP协议不会).一旦客户端获取资源,它将使用信息片断处理有关部分.-------------------------- 上面是URL的九大部分 -----------------------------
 
 URL该如何进行正确的编码?哪些字符可以在URL中安全的使用?
 哪些字符不能使用?

 URL的特殊字符
 -------------------------
 当几种特定的字符集合出现在URL中时,你必须特别注意:
 
首先,在URL中有特殊意义的字符,也就是保留字符:
 ;    /    ?   :     @     &     =     +    $     ,        {10个}
 这意味着,这些字符通常在URL中使用时,是有特殊含义的(如 ":"把每一个部分分隔开来),如果一个URL的某一部分(如查询参数的一部分)可能包含这些字符之一,则应该在放入URL之前对其进行转义处理.
  第二组需要注意的字符集是非保留字符集.如下:
  \-  _   .   !   ~   *   '   (   )             {9个}
 这些字符可以被用于URL的任何位置(有些地方,不允许它们出现.使用它们作为URL的一部分时,你不需要进行编码/转义处理.你可以对它们进行转义操作且不影响URL的语义,但不建议这么做.
第三组  不推荐字符 也就是避用字符集合使用它们是不明智的:
{  }  |   \   ^  [   ]   `::数字1键前::       {8个}
不明智的原因:网关有时会修改这样的字符,或者将其作为分隔符使用.这并不意味着网关总会修改这些字符,但这种情况可能发生.如果真是要使用这些字符,请做转义处理.
第四组   例外字符集  
这组字符集是所有的ASCII控制字符组成.包含空格字符以下列字符:
<   >   #   %   "      {5 个}
控制字符是不可打印的US-ASCII字符(十六进制00~1F及7F)
如果使用,请转义处理.有些字符#(哈希)和%(百分比)在URL上下文中有着特殊含义,你可以把它们当作保留字符对待.这个集合中的其它字符无法被打印,因此对它们进行转义是唯一的表示方式, <   >   "   这三个字符需要被转义，因为这些字符通常用来在文本中分隔URL


编码/转义-------------------------
通常将它的ASCII十六进制值加上一个%字符．
如空格字符的URL编码是　%20
%字符本身被编码为%25

这就是你所需要知道的所有URL的特殊字符，当然，从这些字符外，英文字母
和数字是可以直接使用而不需要进行编码的：）

！！！　必须记住
URL应该始终保持其编码形式．只有当你要拆分URL的时候，才应该对其进行
解码．每个URL部分，都必须分别进行编码．
应该避免重复编码／解码一个URL．如果你编码一个URL一次，但解码两次，而
这个URL包含%字符，那么你将破坏掉你的URL
可以查看　RFC 2396 中的定义

绝对URL和相对URL----------------------------------
如果一个URL包含scheme部分的话（如http），那它就可以被看做绝对URL．
但相对URL就有点复杂了．
相对URL永远是代表着相对于另外一个URL而言的，其它的URL被称为base URL.
将相对URL变成绝对URL的形式，我们首先要了解它的base URL，接着把它跟相
对URL的语法结合之后就得到绝对URL．
在一个HTML文档中看到的相对URL，在这种情况有两个方法找到它的对应的
base URL．
１,base URL可能由html的<base>标签指出．
２,如果没有指定base标签，那么该html文档的url地址就被视为它的base URL.
一旦我们有了base URL ,我们就可以试着将我们的相对URL转成绝对URL.
首先要将相对URL拆分成单个的结构（scheme,authority(host,post),path,query
string,fragment)
拆解完成后，有几个地方需要注意，因为它们可能说明我们转换的相对URL不一定对．
１,如果没有［scheme］［authority］或［path］，那么这个相对URL是base URL的
一个引用．
２,如果有［scheme］，那么这个相对URL其实是绝对URL，所以用绝对URL的方式处理．
３,如果没有［scheme］，但是有［authority(host,post)］，那么我们相对URL可能是网路位址那么我们根据base URL的［模式］并且把它跟相对URL用://结合起来．
４,如果没有上述的这些特殊情形，那我们得到的则是一个完全的相对URL．现在我们需要根据如下的程序进行处理．
５,我们从base URL继承它的scheme以及authority(host,post).
６,如果相对URL开头是一个/，那么它是一个绝对路径，我们可以通过继承得到的authoruity和host，用适当的分隔号产生绝对url
７,如果相对URL的开头不是一个/，那我们把base URL中／以后的东西都移除．然后把我们相对URL加上去得到最后的路径，再根据前面的几个字符对相对URL做一点小处理．
８,如果最后的路径包含./　　
我们直接把它去掉（这代表我们的相对URL由./开始，就像./blah.html)
９,如果最后的路径包含../　　
我们可以去掉它并且把路径上移一个区段，就好像把所有像样的路径组合都去掉＂＜segment＞/../＂,持续这个过程，直到找不到任何的../这表示我们的相对路径开头是多个../像是../blah.htm或../../blah.html
１０,如果路径的结尾是.. ,那就去掉它并且把路径上移一个区段，比如移除""<segment>/../"
 这表示我们的相对路径是．．
 １１,如果路径的结尾是．，那就把它去掉，表示我们的相对路径很有可能是．
 根据这些规则我们可以简单把相对URL的query或者fragment利用合适的分隔符号
 包含到我们的URL中去，这样就将相对URL转换成了绝对URL．

 范例：
 base:  http://www.blah.com/yadda1/yadda2/yadda3?param1=foo#barrelative:rel1
 final  :  http://www.blah.com/yadda1/yadda2/rel1

 base:  http://www.blah.com/yadda1/yadda2/yadda3?param1=foo#barrelative:/rel1
 final :   http://www.blah.com/rel1

 base:  http://www.blah.com/yadda1/yadda2/yadda3?param1=foo#barrelative:../rel1
 base:  http://www.blah.com/yadda1/rel1

 base:  http://www.blah.com/yadda1/yadda2/yadda3?param1=foo#barrelative:./rel1?param2=baz#bar2
 final:    http://www.blah.com/yadda1/yadda2/rel1?param2=baz#bar2

base:    http://www.blah.com/yadda1/yadda2/yadda3?param1=foo#barrelative: ..
 final :    http://www.blah.com/yadda1/
