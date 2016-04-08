title: TCP/IP详解笔记(8)
date: 2015-05-13 15:48:05
tags: TCP/IP
---

广播、多播 IGMP协议 DNS协议

<!-- more-->

>单播是指两台主机的点对点，广播和多播则是一对多。
广播和多播仅用于UDP


## 广播：

四种广播地址：

>1. 受限的广播：255.255.255.255 改地址用于主机配置过程中IP数据报的目的地址。
2. 指向网络的广播：A类网络广播地址为neid.255.255.255 neid为A类网络的网络号。
3. 指向子网的广播：主机号全为1且有特定子网号的地址。例如128.1.2.255 子网掩码为255.255.255.0.
4. 指向所有子网的广播：子网号和主机号都为1。例如128.1.255.255 子网掩码为255.255.255.0.


![](http://img.blog.csdn.net/20150406131443980?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


这是我的IP，和子网掩码，用舍友的电脑ping 192.168.1.255
用wireshark抓包


![](http://img.blog.csdn.net/20150406131406496?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

可以看到链路层的目的地址广播地址，用的是UDP协议。
![](http://img.blog.csdn.net/20150406131456273?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 多播：

利用多播可以减轻对广播的数据不敢兴趣的主机的负担
IP多播提供两种类型的服务：

1.         向多个目的地址传送数据
2.         客户对服务器的请求。（后面的BOOTP）

多播过程：![](http://img.blog.csdn.net/20150406131417151?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

一台主机发送数据报，当目的IP为D类地址时就代表向一个多播组内的所有主机进行广播。但是我们知道发送数据是需要知道目的mac地址的，因此一台主机在除了存在一个mac地址外 还存在一个多播地址。通过将其D类IP地址的低位23bit映射到相应以太网地址中便可实现多播组地址到以太网地址的转换。
 
同时需要一个协议让多播路由器了解确定网络中属于特定多播组的任何一个主机，这就需要IGMP协议。

### IGMP协议：
IGMP是IP层的协议，当一台主机的某个进程加入一个多播组后会发送IGMP报告报文，，离开一个组时不发送，只是当IGMP查询报文到来时不响应。多播路由器会定时发送IGMP查询报文来确定多播组里的主机
 
### DNS域名系统：
我们平时上网用的每个网址都对应着一个IP地址，如何得到网址到IP地址的映射则需要DNS，

应用程序通过名字解析器向本地名字服务器发送DNS查询请求，这个本地名字服务器可以是某个根名字服务器也可以是其它名字服务器

DNS即使用UDP也使用TCP协议。

例子：

![](http://img.blog.csdn.net/20150406131421488?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Rlogin客户端想要根据Rlogin服务器的主机名来访问需要的步骤：


1.         Rlogin客户向根名字服务器发送一个查询请求
2.         名字服务器告诉客户端Rlogin服务器所在的域服务的名字服务器名
3.         客户端的名字解析器向该服务器的名字服务器重发请求。
4.         得到的应答包括Rlogin服务器的IP地址。
5.         之后的与前面类似