---
title: fetch、axios的跨域配置
date: 2022-05-29 16:28:03
categories: 前端
---
## fetch默认不携带cookie
etch发送请求默认是不发送cookie的，不管是同域还是跨域；那么问题就来了，对于那些需要权限验证的请求就可能无法正常获取数据，这时可以配置其credentials项，其有3个值：

- omit: 默认值，忽略cookie的发送
- same-origin: 表示cookie只能同域发送，不能跨域发送
- include: cookie既可以同域发送，也可以跨域发送
- credentials所表达的含义，其实与XHR2中的withCredentials属性类似，表示请求是否携带cookie；

fetch默认对服务端通过`Set-Cookie`头设置的`cookie`也会忽略，若想选择接受来自服务端的`cookie`信息，也必须要配置`credentials`选项

## axios 中配置withCredentials
**配置withCredentials:**

```
const service = axios.create({  
  baseURL: process.env.VUE_APP_BASE_API, // 环境变量base接口地址 url = base url + request url  
  withCredentials: true, // 跨域请求时发送Cookie  
  timeout: 60000, // 请求超时  
  headers: {    "Content-Type": "application/json; charset=UTF-8;"  }});
```

需要注意是，当配置了xhr.withCredentials = true时，必须在后端增加 response 头信息Access-Control-Allow-Origin，且必须指定域名，而不能指定为*。

如果在同域下配置xhr.withCredentials，无论配置true还是false，效果都会相同，且会一直提供凭据信息(cookie、HTTP认证及客户端SSL证明等)

**`Access-Control-Allow-Credentials`** 响应头表示是否可以将对请求的响应暴露给页面。返回true则可以，其他值均不可以。

`Access-Control-Allow-Credentials` 头 工作中与`XMLHttpRequest.withCredentials` 或Fetch中的`Request()` 构造器中的`credentials` 选项结合使用。
Credentials必须在前后端都被配置（即the `Access-Control-Allow-Credentials` header 和 XHR 或Fetch request中都要配置）才能使带credentials的CORS请求成功。
