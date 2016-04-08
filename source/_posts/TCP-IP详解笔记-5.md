title: TCP/IP详解笔记(5)
date: 2015-04-22 15:48:05
tags: TCP/IP
---

ICMP协议、Ping程序、Traceroute程序
<!--more-->

## ICMP协议、Ping程序、Traceroute程序

ping程序和Traceroute程序都是基于ICMP协议的，放在一起看。
ICMP是网络层的协议，尽管它是封装在IP数据报内部。

![](http://img.blog.csdn.net/20150320105915754?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


### ICMP报文格式：


8位类型字段区分类型，代码区分类型（如下图），16位检验和覆盖ICMP的头部和数据部分。

![](http://img.blog.csdn.net/20150320105923211?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


![](http://img.blog.csdn.net/20150320105858002?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

根据上图可以看到 ICMP分为查询和差错两种，注意：ICMP差错报文出错时不会再产生ICMP差错报文，不然会一直循环下去。
同时目的地址是广播地址或多播地址的IP数据报也不会产生，如果产生的话 会产生很多ICMP差错报文，会产生广播风暴。

### ICMP地址掩码请求与应答：

![](http://img.blog.csdn.net/20150320105901964?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

无盘系统通过广播此数据报根据ICMP应答获得子网掩码。

标识符和序列号由发送端任意设定，在应答中会返回，发送端就可以根据这个进行匹配
 
### ICMP时间戳请求与应答

![](http://img.blog.csdn.net/20150320105935207?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

用于向另一个系统查询当前的时间，返回的值是自午夜开始算的毫秒数，无法获知当时的日期。

### ICMP端口不可达差错


![](http://img.blog.csdn.net/20150320105939996?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

当目的端口不可达时，产生一个ICMP差错报文，一般的差错报文都会包含IP首部，及数据段的前8个字节，而这前8个字节主要包含了端口信息。通过这个可以了解导致差错的原因

### Ping程序：

Ping程序大部分人都用过，它的原理就是发送ICMP回显请求，对方收到后发送icmp回显应答，以此判断是否可达。大多数支持TCP/IP协议的都会支持


![](http://img.blog.csdn.net/20150320105943647?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150320105925442?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

可以看到第一次时间比较久，这很可能是发送arp请求和接收应答所花费的时间

![](http://img.blog.csdn.net/20150320105929779?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150320110003131?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

从wireshark中我们可以看到请求携带的数据，应答也都回显了，报文的格式和上面的图一样。
 
 
### IP记录路由：

![](http://img.blog.csdn.net/20150320110007281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

在笔记（4）中提到IP的选项字段最多只有40个字节，除去3个字节的code(指明这个选项是干什么的)len(长度)ptr(指针字段)剩下的只够9个IP地址的。因此最多存放9个IP地址。


从下面的图也可看出来

![](http://img.blog.csdn.net/20150320110011430?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150320110018419?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150320110023723?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

抓到的包里也可以看出。
 
IP时间戳选项类似。

### Traceroute程序：

因为IP记录路由选项记录的路由地址最多为9个，

无法完全呈现路由路径。

Traceroute可以完全呈现路径，原理是利用ip首部的TTL字段 TTL字段每经过一个路由器就会-1，到0时，路由器会向发送端发送ICMP超时报文（包含该路由的IP）。因此可以从1开始逐步加1，当数据报到达时产生一个端口不可达错误，就知道到目的主机了。


 
### IP源站选项路由：
- 严格的源路由选择：在IP首部的选项段中添加路由地址，发送端指明IP数据报必须采用指定的路由，当一个路由器发现下一站路由不在其相连的网站上 就返回一个源站路由失败的 ICMP差错报文
- 宽松的源站选路:发送端指明了一个数据报经过的IP地址清单，但是数据报在清单上指明的任意两个地址之间可通过其他

本节完