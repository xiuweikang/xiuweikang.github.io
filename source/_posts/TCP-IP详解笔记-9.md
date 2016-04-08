title: TCP/IP详解笔记(9)
date: 2015-05-13 15:48:05
tags: TCP/IP
---
TFTP简单文本传输协议和BOOTP引导程序协议
<!--more -->

###TFTP协议
TFTP主要使用UDP协议，当需要将程序或文件同时向许多机器下载时就需要TFTP。或者当无盘系统工作时先广播一TFTP请求，网络上的TFTP服务器就会发送响应，其中包含可执行的程序，依靠这个无盘系统来执行程序。


![](http://img.blog.csdn.net/20150406134721755?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

同时为了允许多个客户并发的进行系统引导，TFTP通过为每个客户分配一个端口号来实现并发。
TFTP协议没有提供安全性。


###BOOTP引导程序协议：
在ARP协议时说过可以通过RARP来得到自己的IP，但这只能得到IP地址，且该子网下必须存在RARP服务器。

BOOTP引导程序协议则是通过UDP和TFTP来实现无盘系统的引导。


![](http://img.blog.csdn.net/20150406134808256?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


一个路由器能够作为真正BOOTP服务器的代理，向位于不同网络的真正BOOTP服务器转发客户的BOOTP请求。

