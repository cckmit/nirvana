---
title: Nginx部署配置
date: 2022-07-08 16:28:03
categories: linux
---
常用命令
启动：
`nginx`
立即停止：
`nginx -s stop`
优雅停止：
`nginx -s quit`
重启
`nginx -s reload`
查看配置文件是否正常，无语法错误
`nginx -t`
查看日志
```
tail -f /var/log/nginx/access.log
upstream
基本语法
```
upstream 的基本语法如下，一个 upstream 需要设置一个名称，这个名称可以在 server 里面当作 proxy 主机使用。
```
upstream ws {
     server 127.0.0.1:8080;
}
```
一个 upstream 可以设置多个 server ，通常情况下 Nginx 会轮询每一个 server，从而达到最基本的负载循环效果。
```
upstream default {
    server  tflinux_php-fpm-tfphp_1:9000;
    server  tflinux_php-fpm-tfphp_2:9000;
}]
```
其他
还可以配置一些 max_fails（最多出错数量）、fail_timeout（故障等待超时时间）、backup（备用服务器参数）等等，不多做介绍，用到的时候可以查一下。

配置http
进入 nginx 目录，配置 /etc/nginx/nginx.conf，主要是确认你要存放的配置文件有没有被引入（include）进来，还有就是配置 upstream，这样你在项目的 .conf 文件中可以直接引用。
```
http {

    # 省略其他一些默认配置
    
    # 单纯使用一个server的时候有点类似在全局定义了哥别名-ws,后面你在子配置文件里使用http://ws/就是等价http://127.0.01:8080
    upstream ws {
        server 127.0.0.1:8080;
    }

    # ....
    include /etc/nginx/sites-enabled/*;
}
无证书
server {
        # 域名，没有域名就不用写这行，直接用ip访问
    server_name dev.xxx.com;
    # 监听的端口，访问时通过 ip:9099 访问
    listen 9099;
    # gzip 压缩，加快传输效率，有利于页面首次渲染
    gzip on;
    gzip_min_length 1k;
    gzip_comp_level 9;
    gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;
    gzip_vary on;
    gzip_disable "MSIE [1-6]\.";
        
        # 指定项目入口文件index.html存放的位置
    root /home/xxx/my-project/dist;
    
    # 配置反向代理，路径中匹配到了/api开头，就转发到http://127.0.0.1:8888下，所以你本地发的请求就不需要带端口了
    location /api {
        proxy_set_header Host $http_host;
        # 这里也可以使用upstream，然后用它的名字代替，都一样，看你自己
        proxy_pass http://127.0.0.1:8888;
    }
    # 配置重定向，防止页面刷新404
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```
配置ssl证书
申请 ssl 证书
把证书copy到 nginx 目录下,比如我放在了一个叫 ca 的目录，一个 .cer 文件，一个 .key 文件，一共两个文件
配置 .conf文件
```
server {
    server_name ghostwang.xxx.com;
    # 注意这里是443端口
    listen 443 ssl;

    # gzip config
    gzip on;
    gzip_min_length 1k;
    gzip_comp_level 9;
    gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;
    gzip_vary on;
    gzip_disable "MSIE [1-6]\.";

        # 证书配置相关
        # cer文件存放目录
    ssl_certificate /etc/nginx/ca/xxx.xxx.com.cer;
    # key文件存放目录
    ssl_certificate_key /etc/nginx/ca/xx.xx.com.key;
    # ssl其他的一些配置...
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4:!DH:!DHE;
    ssl_prefer_server_ciphers on;

    root /var/www/my-project/dist;
```
