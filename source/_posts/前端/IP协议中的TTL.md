---
title: 淘宝NPM镜像站切换新域名
date: 2021-12-05 21:12:40
category: javascript
---
## 定义
**TTL**是IP协议包中的一个值，指定数据报被路由器丢弃之前允许通过的网段数量。(IP数据包在计算机网络中可以转发的最大跳数)

在很多情况下数据包在一定时间内不能被传递到目的地。解决方法就是在一段时间后丢弃这个包，然后给发送者一个报文，由发送者决定是否要重发。

TTL 是由发送主机设置的，以防止数据包不断在 IP 互联网络上永不终止地循环。转发 IP 数据包时，每经过一个路由器，路由器会修改TTL值， 即将改值减小1。当记数到0时，路由器决定丢弃该包，并发送一个*`ICMP Type 11 and Code 0 message(Time to live exceeded) `*报文给最初的发送者，由发送者决定是否要重发。

##  常见操作系统的TTL值
>UNIX 及类 UNIX 操作系统       ICMP 回显应答的 TTL 字段值为 255
Compaq Tru64 5.0             ICMP 回显应答的 TTL 字段值为 64
微软 Windows NT/2K操作系统    ICMP 回显应答的 TTL 字段值为 128
微软 Windows 95 操作系统      ICMP 回显应答的 TTL 字段值为 32
LINUX Kernel 2.2.x & 2.4.x   ICMP 回显应答的 TTL 字段值为 64

## linux系统TTL值修改
TTL值在文件`/proc/sys/net/ipv4/ip_default_ttl`中定义,可通过执行`echo 128 > /proc/sys/net/ipv4/ip_default_ttl`命令修改
(这是短暂性的)若要永久生效可修改`/etc/sysctl.conf`配置文件，添加`net.ipv4.ip_default_ttl=128`，接着执行`sysctl -p`即可。

## 理解发送主机
在本机(windows 10)ping本地的VMware虚拟主机(操作系统为CentOS release 6.8)，其IP为192.168.10.128，可见TTL为64：
![](https://upload-images.jianshu.io/upload_images/10024246-b4afcfbcee4282fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在CentOS上执行`echo 168 > /proc/sys/net/ipv4/ip_default_ttl`修改TTL值为168，接着再次在本机(windows 10)ping 192.168.10.128，发现TTL由64变为168
![](https://upload-images.jianshu.io/upload_images/10024246-310364058a6ab30a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-7f502d8d6c2225a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
综上可知，这里的发送主机指的是ping后面IP对应的主机。