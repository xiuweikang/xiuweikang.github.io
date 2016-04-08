title: TCP/IP详解笔记(7)
date: 2015-05-12 15:48:05
tags: TCP/IP
---
UDP：用户数据报协议
<!--more -->


![](http://img.blog.csdn.net/20150330083313392?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

UDP将应用层发的数据加上首部进行封装，提供不可靠的的服务。

### UDP首部：

![](http://img.blog.csdn.net/20150330083356554?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

UDP首部包含2字节的源端口和目的端口，16位的UDP长度则代表UDP数据报的长度（包含UDP首部）所以该值最小为8个字节，数据为空。


### UDP检验和：

首部中需要注意的是UDP的检验和和之前IP、ICMP的检验和是类似但是是有区别的，UDP数据报可以是奇数字节，最后不满16bit的时候需要增加填充字节0.只是为了计算，可以不传送。


![](http://img.blog.csdn.net/20150330083402092?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

同时，UDP和TCP都包含一个伪首部包含源IP和目的IP。目的是让UDP两次检验是否正确到达。当产生差错时，接收方不会产生任何差错报文，UDP检验和是可选的。

### IP分片：

IP分片是在网络层发生的。由于MTU的存在，当IP层收到上层发下来的数据报时，进行封装加上IP首部，得到的IP数据报长度要<=该接口的MTU。路径MTU则为路径上最小的MTU，遇到时也需要分片。注意之前提到的IP首部中的标志字段在这里表示是否后面有更多的片，偏移量也是用于组装用的。


通过netstat可看本机接口mtu

![](http://img.blog.csdn.net/20150330083413542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150330083329740?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


如上图，应用层发1473字节的数据，加上UDP首部一共1481个字节，前面IP（无选项字段）20字节，一共1501字节，因为最大MTU为1500，，因此数据部分被成两片，其中UDP首部在第一片，因此抓到的包只有在第一片才显示端口信息。

### ICMP不可达差错：
如果路由器收到一份需要分片的报文，但是报文中的标志字段设置成了不可分片，这时就会发送一份ICMP不可达差错报文，通过这个特点就可以知道路径MTU。

![](http://img.blog.csdn.net/20150330083418690?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

通过Traceroute也可确定路径MTU。发送不能分片的数据报，每次收到差错报文时，减少数据报长度。
### ICMP源站抑制差错
当报文的处理速度比其收到报文的速度慢时则可向发送端发送ICMP源站抑制差错报，用于控制发送端发送数据。
### 使用UDP的服务器设计
和TCP的三次握手建立连接4次挥手释放连接不同的是，用UDP作为通信协议的服务器平时是睡眠状态的，当一个数据报到达时唤醒。
UDP服务器如果在端口号之前指定一个IP地址，那么该IP地址就称为该端点的本地IP地址，即只要目的IP等于本地IP时，UDP数据报才能被送到该端点。
同时可以在相同的端口上启动不同的服务器，但是每个服务器要具有不同的本地IP地址。
使用UDP的服务器可以限制远端IP地址，即该端口号不接受被限制的远端IP 