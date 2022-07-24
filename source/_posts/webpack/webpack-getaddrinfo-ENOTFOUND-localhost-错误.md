---
title: webpack getaddrinfo ENOTFOUND localhost错误
date: 2021-03-11 21:12:40
categories: webpack
---

原因 没有指定 localhost 
在host 文件中添加 
127.0.0.1 localhost

```
events.js:141
      throw er; // Unhandled 'error' event
      ^

Error: getaddrinfo ENOTFOUND localhost
    at errnoException (dns.js:27:10)
    at GetAddrInfoReqWrap.onlookup [as oncomplete] (dns.js:78:26)
```
