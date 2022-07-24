---
title: 跨域请求出现preflight request失败的问题的解决
date: 2021-11-17 16:13:30
categories: 前端
---
# 问题出现

这两天在项目联调过程中突然前端同学报告出现CORS跨域问题无法访问。刚听到很奇怪，因为已经在项目里面设置了CORS规则，理论上不会出现这个问题。

```
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {
        String orignalHeader = request.getHeader("Origin");

        if (orignalHeader != null ) {
            Matcher m = CORS_ALLOW_ORIGIN_REGEX.matcher(orignalHeader);
            if (m.matches()) {
                response.addHeader("Access-Control-Allow-Origin", orignalHeader);
                response.addHeader("Access-Control-Allow-Credentials", "true");
                response.addHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT");
                response.addHeader("Access-Control-Allow-Headers", "x-dataplus-csrf, Content-Type");
            }
        }
    }
```

拿到前端给的错误提示后发现了一个奇怪的问题，提示Response to preflight request doesn't pass access control check中的preflight request是什么？

![](https://upload-images.jianshu.io/upload_images/10024246-5ecb15a85b51ce57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "image.png")

# Preflight request介绍

了解得知跨域资源共享标准新增了一组 HTTP 首部字段，允许服务器声明哪些源站通过浏览器有权限访问哪些资源。同时规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（包括 Cookies 和 HTTP 认证相关数据）。

一个Preflight request的流程可以如下图所示

![image.png](https://upload-images.jianshu.io/upload_images/10024246-9d82d589476ef5e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "image.png")

什么样的请求会产生Preflight request呢？当请求满足下述任一条件时，即应首先发送Preflight request请求：

*   使用了下面任一 HTTP 方法：

    *   PUT
    *   DELETE
    *   CONNECT
    *   OPTIONS
    *   TRACE
    *   PATCH
*   人为设置了对 CORS 安全的首部字段集合之外的其他首部字段。该集合为：

    *   Accept
    *   Accept-Language
    *   Content-Language
    *   Content-Type (需要注意额外的限制)
    *   DPR
    *   Downlink
    *   Save-Data
    *   Viewport-Width
    *   Width
*   Content-Type 的值不属于下列之一:

    *   application/x-www-form-urlencoded
    *   multipart/form-data
    *   text/plain
*   请求中的XMLHttpRequestUpload 对象注册了任意多个事件监听器。
*   请求中使用了ReadableStream对象。

在我们的例子中正是使用了POST方法传递了一个Content-Type为application/json的数据到后端。而这个OPTION请求返回失败后浏览器并没有继续下发POST请求。

# 解决方案

在弄清楚问题后，我们了解只要给Preflight request优先通过就可以引导后续请求继续下发。对此，我们改造CORS Filter来解决这个问题。

*   首先对OPTION请求放入HTTP 200的响应内容。
*   对于Preflight request询问中的的Access-Control-Request-Headers予以通过

```
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {
        String orignalHeader = request.getHeader("Origin");

        if (orignalHeader != null ) {
            Matcher m = CORS_ALLOW_ORIGIN_REGEX.matcher(orignalHeader);
            if (m.matches()) {
                response.addHeader("Access-Control-Allow-Origin", orignalHeader);
                response.addHeader("Access-Control-Allow-Credentials", "true");
                response.addHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT");
                response.addHeader("Access-Control-Allow-Headers", request.getHeader("Access-Control-Request-Headers"));
            }
        }

        if ("OPTIONS".equals(request.getMethod())) {
            response.setStatus(HttpServletResponse.SC_OK);
        } else {
            filterChain.doFilter(request, response);
        }
    }
```

# 注意事项

但是天不遂人愿，在上述改造后理论上应该是可以解决Preflight request问题，可以测试发现依然有问题。这时我们注意到错误信息中提到的另外一句Redirect is not allowed for a preflight request.

为什么会有Redirect事情发生呢，原来所有请求在进入我们的CORS Filter之前，会首先通过SSO Filter做登录检测。而这个Preflight request并没有携带登录信息，导致OPTION请求被跳转到了登录页面。同理如果引用了Spring Security组件的的话也会出现首先被登录验证给过滤的问题。

找到问题就比较好办了，调整CORS Filter优先级，让其先于登录验证进行就好了。对此我们调整registrationBean的order从默认的Integer.MAX_VALUE到1就好了。

```
    @Bean(name = "corsFilter")
    public FilterRegistrationBean corsFilter() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        registrationBean.setFilter(corsFilterBean());
        registrationBean.setUrlPatterns(Lists.newArrayList("/*"));
        registrationBean.setOrder(1);
        return registrationBean;
    }
```
