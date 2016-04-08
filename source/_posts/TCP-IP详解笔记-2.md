title: TCP/IP详解笔记(2)
date: 2015-04-3 15:48:05
tags: TCP/IP
---

链路层相关协议 ：以太网封装  SLIP协议  CSLIP协议  PPP协议
<!-- more-->

![](http://img.blog.csdn.net/20150227160817052?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

可以从图中看出链路层的三个目的：

>1. 为IP模块发送和接收数据报
2. 为ARP模块发送ARP请求和ARP应答
3. 为RARP发送RARP请求和接收RARP应答

## 以太网和IEEE封装

![](http://img.blog.csdn.net/20150227161030991?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图上面的是IEEE的封装 下面的是以太网的封装，我们主要看下面的：
链路层将来自上层的数据报封装成帧，可以看到类型有3个可选项，这是为分用准备的，CRC是用来差错检验的。ARP和RARP后面的PAD是因为以太网封装的数据部分最少是48个字节，不够的添加PAD补足，加上前面的地址部分所有以太网发送的帧最少是60字节，这一步是在最后做的 tcpdump是无法看到的。（后面会提到）

![](http://img.blog.csdn.net/20150227161057339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


## SLIP：串行线路：
这是一种对IP数据报简单的封装。在对IP数据报的开头和结尾都加上END字符 这样就完成了封装。如果数据报中含有END字符则要将其转义，不细说了。

SLIP的封装很简单，但是有缺陷：

>1. 每一端必须知道对方的IP地址，没有办法把本端的IP地址告诉另一端
2. 没有类型字段，一条串行线路用于SLIP后，那它无法使用其他协议。
3. 无CRC差错检验

**CSLIP**：为压缩的SLIP线路主要将TCP首部20字节和IP首部20自字节压缩到3-5个字节


### PPP协议：与SLIP最大不同的是加了协议字段和CRC

## 环回接口：

在上一章提到过了：大部分系统是127.0.0.1 且命名为localhost

![](http://img.blog.csdn.net/20150227161126467?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


这张图在后面也用到，挺重要的。 根据这张图可以看到它没有省略运输层和网络层 是在链路层的时候发现然后在传给自己的，这样做是为了简化设计

图中的关键点：

>1. 传给环回地址的任何数据均作为IP输入
2. 传给广播或多播的数据复制一份给环回接口， 也就是说该主机发送的广播，自己是能收到的
3. 任何传给该主机IP地址的数据均送到环回地址。

## 最大传输单元MTU

以太网对其数据帧长度有要求不得超过1500字节，这个就为其MTU，两台主机通信路径中最小的MTU称为路径MTU 我们可以在用命令netstst –in打印出


![](http://img.blog.csdn.net/20150227161141459?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


第二章主要讲了链路层的几种协议，以及他们对数据报的封装。
