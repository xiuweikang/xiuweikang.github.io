title: TCP/IP详解笔记(1)
date: 2015-04-2 15:48:05
tags: TCP/IP
---
TCP/IP详解概述
<!-- more-->

这里是《TCP/IP详解》卷一的一些笔记以及自己对书中的理解，我会结合抓包软件Wireshark来分析，但由于条件有限，无法模拟出书中作者的网络，只能简单的分析，如果发现错误，请帮忙指正。
 
首先我们讲什么是TCP/IP协议：
>两台主机要通信必须遵循相同的规范，对互相发送的报文要知道该如何处理，就像发送XML和JSON数据一样，他们需要按照预先规定的格式组织进行发送，而接收端也要按照这种格式来解读。网络通信协议做的就是这个，而tcp/ip就是协议中用的最广泛的一种。
 
TCP/IP的分层使得协议变的更加好理解，每一层各司其职，所以在这里我在看某一层的协议时就不要管下一层是怎么样的。例如：我们在看TCP协议时就不要管下面的IP是怎么样的，你所要注意的只是端到端的通信，这样可以简化我们的思维，使我们更专注这一层是怎样工作的。  

![](http://img.blog.csdn.net/20150227113055582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**不同于osi模型的7层，tcp/ip只有4层：**

>链路层：也叫数据链路层或网络接口层，它作为最底层，它包含设备驱动程序（TCP/IP中的是以太网驱动程序），和计算机中的网卡，主要处理物理细节。
 

>网络层：处理数据报在网络中的活动，例如选路。我们常用的路由器最高到这一层，而选路分组转发也主要由路由器中的这层完成的。在这层有主要由IP,ICMP,IGMP协议，后面我们会一一讲到。
 
>运输层：主要为我们提供两台主机上端到端的通信。含可靠 的TCP和不可靠的UDP
 
>应用层：负责处理应用程序的细节。

### 互联网中的地址：

互联网的每个接口必须有唯一的一个ip地址，（一台主机可以有不止一个接口）windows用户可以用ipconfig命令查看，linux下用ifconfig，长为32bit在今天我们的ip地址大部分都是动态分配的。
我们可以看到我的电脑中有两个接口eth0和lo 其中lo是本地环回接口，地址为127.0.0.1，我们连接本地服务器时经常走的这个接口。


![](http://img.blog.csdn.net/20150227113139930?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


这是我们常用的点分十进制表示法，其中每个类的网络号个数都要-2，全0和全1是非法的

![](http://img.blog.csdn.net/20150227113200507?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150227113211754?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


### 数据的封装：

![](http://img.blog.csdn.net/20150227113206656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

从图中我们可以看出来，发送端用户数据在经过每一层都加上了相应的首部，而每个首部都含有相应的协议标识（后面会讲到），依次封装，而接收端则按照这个标识来判断分别是哪一个协议，来进行分用，一层一层的拆分。如下图：

![](http://img.blog.csdn.net/20150227143940063?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGl1d2Vpa2FuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


第一章主要是概述TCP/IP协议，接下来我们会从底向上根据每一层相应的协议一层一层的分析
 