---
title: Web 存储方案简介
date: 2021-06-10 16:47:58
categories: 前端
---
##  Cookie
HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。

##### Cookie 主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）；
- 个性化设置（如用户自定义设置、主题等）；
- 浏览器行为跟踪（如跟踪分析用户行为等）。
##### Cookie 的特点：

- Cookie 的大小受限，一般为 4 KB；
- 同一个域名下存放 Cookie 的个数是有限制的，不同浏览器的个数不一样，一般为 20 个；
- Cookie 支持设置过期时间，当过期时自动销毁；
- 每次发起同域下的 HTTP 请求时，都会携带当前域名下的 Cookie；
- 支持设置为 HttpOnly，防止 Cookie 被客户端的 JavaScript 访问。
##### 示例1：简单用法
```
document.cookie = "name=semlinker";
document.cookie = "favorite_food=tripe";

alert(document.cookie);
// 显示: name=semlinker;favorite_food=tripe
```
##### 示例2：得到名为 test2 的 cookie
```
document.cookie = "test1=Hello";
document.cookie = "test2=World";

var myCookie = document.cookie
    .replace(/(?:(?:^|.*;\s*)test2\s*\=\s*([^;]*).*$)|^.*$/, "$1");
alert(myCookie);
```
## localStorage
一种持久化的存储方式，也就是说如果不手动清除，数据就永远不会过期。它是采用键值对的方式存储数据，按域名将数据分别保存到对应数据库文件里。相比 Cookie 来说，它能保存更大的数据。

##### localStorage 的特点：

- 大小限制为 5MB ~10MB；
- 在同源的所有标签页和窗口之间共享数据；
- 数据仅保存在客户端，不与服务器进行通信；
- 数据持久存在且不会过期，重启浏览器后仍然存在；
- 对数据的操作是同步的。
#####示例
```
// 通过setItem()增加一个数据项
localStorage.setItem('myName', 'Semlinker');

// 通过getItem()获取某个数据项
let me = localStorage.getItem('myName');

// 通过removeItem()移除某个数据项
localStorage.removeItem('myName');

// 移除所有数据项
localStorage.clear();
```
## sessionStorage
与服务端的 session 类似，sessionStorage 是一种会话级别的缓存，关闭浏览器时数据会被清除。需要注意的是 sessionStorage 的作用域是窗口级别的，也就是说不同窗口之间保存的 sessionStorage 数据是不能共享的。

##### sessionStorage 的特点：

- sessionStorage 的数据只存在于当前浏览器的标签页；
- 数据在页面刷新后依然存在，但在关闭浏览器标签页之后数据就会被清除；
- 与 localStorage 拥有统一的 API 接口；
- 对数据的操作是同步的。
##### 示例
```
// 通过setItem()增加一个数据项
sessionStorage.setItem('myName', 'Semlinker');

// 通过getItem()获取某个数据项
let me = sessionStorage.getItem('myName');

// 通过removeItem()移除某个数据项
sessionStorage.removeItem('myName');

// 移除所有数据项
sessionStorage.clear();
```
## Web SQL
Web SQL 数据库 API 实际上不是 HTML5 规范的一部分，而是一个单独的规范，它引入了一组 API 来使用 SQL 来操作客户端数据库。需要注意的是，HTML5 已经放弃 Web SQL 数据库。

Web SQL Database 规范中定义的三个核心方法：

- openDatabase：这个方法使用现有数据库或新建数据库来创建数据库对象；
- transaction：这个方法允许我们根据情况控制事务的提交或回滚；
- executeSql：这个方法用于执行真实的 SQL 语句。
##### Web SQL 的特点（相比 Cookie、localStorage 与 sessionStorage）：

- Web SQL 能方便进行对象存储；
- Web SQL 支持事务，能方便地进行数据查询和数据处理操作。
##### 示例
```
var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024);

db.transaction(function (tx) { 
   // 执行查询操作
   tx.executeSql('CREATE TABLE IF NOT EXISTS LOGS (id unique, log)'); 
   // 执行插入操作
   tx.executeSql('INSERT INTO LOGS (id, log) VALUES (1, "foobar")'); 
   tx.executeSql('INSERT INTO LOGS (id, log) VALUES (2, "logmsg")'); 
});
```
## IndexedDB
IndexedDB 是一种底层 API，用于客户端存储大量结构化数据，包括文件、二进制大型对象。该 API 使用索引来实现对该数据的高性能搜索。虽然 Web Storage 对于存储较少量的数据很有用，但对于存储更大量的结构化数据来说，这种方法不太好用。IndexedDB 提供了一个解决方案。

##### IndexedDB 的特点：

- 存储空间大：存储空间可以达到几百兆甚至更多；
- 支持二进制存储：它不仅可以存储字符串，而且还可以存储二进制数据；
- IndexedDB 有同源限制，每一个数据库只能在自身域名下能访问，不能跨域名访问；
- 支持事务型：IndexedDB 执行的操作会按照事务来分组的，在一个事务中，要么所有的操作都成功，要么所有的操作都失败；
- 键值对存储：IndexedDB 内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括 JavaScript 对象。对象仓库中，数据以 “键值对” 的形式保存，每一个数据记录都有对应的主键，主键是独一无二的，不能有重复，否则会抛出一个错误。
- 数据操作是异步的：使用 IndexedDB 执行的操作是异步执行的，以免阻塞应用程序。
##### 示例
```
var dbName = "my_db";

var request = indexedDB.open(dbName, 2);

request.onerror = function(event) {
  // 错误处理
};

request.onupgradeneeded = function(event) {
  var db = event.target.result;

  // 建立一个对象仓库来存储我们客户的相关信息，我们选择 ssn 作为键路径（key path）
  // 因为 ssn 可以保证是不重复的
  var objectStore = db.createObjectStore("customers", { keyPath: "ssn" });

  // 建立一个索引来通过姓名来搜索客户。名字可能会重复，所以我们不能使用 unique 索引
  objectStore.createIndex("name", "name", { unique: false });

  // 使用邮箱建立索引，我们确保客户的邮箱不会重复，所以我们使用 unique 索引
  objectStore.createIndex("email", "email", { unique: true });

  // 使用事务的 oncomplete 事件确保在插入数据前对象仓库已经创建完毕
  objectStore.transaction.oncomplete = function(event) {
    // 将数据保存到新创建的对象仓库
    var customerObjectStore = db.transaction("customers", "readwrite").objectStore("customers");
    customerData.forEach(function(customer) {
      customerObjectStore.add(customer);
    });
  };
};
```