title: '从Decorator,Adapter模式看Java/IO库'
date: 2014-09-24 15:42:18
tags: java
---
java I/O库中的设计模式
<!-- more -->

讲到Decorator模式的时候,不能不提到它的实际应用－－在Java/IO库里面的应用，<<Java与模式>>这本书也不例外，有点不一样的是，这本书在介绍的时候有个专题，是从两个模式来看Java/IO库，完这个专题后，个人感觉对Java/IO库有了全新的认识同时也加深了Decorator模式跟Adapter适配器模式的理解，现和大家分享下这个在我看来很伟大的成果，同时说明下，以下大部分文字跟图片是来自<<Java与模式>>这本书。


### 一.引子（概括地介绍Java的ＩＯ）
　无论是哪种编程语言，输入跟输出都是重要的一部分，Java也不例外，而且Java将输入／输出的功能和使用范畴做了很大的扩充。它采用了流的机制来实现输入／输出，所谓流，就是数据的有序排列，而流可以是从某个源（称为流源或Source of Stream）出来，到某个目的地（称为流汇或Sink of Stream）去的。由流的方向，可以分成输入流和输出流，一个程序从输入流读取数据向输出流写数据。
　如，一个程序可以用FileInputStream类从一个磁盘文件读取数据，如下图所示：

![](http://dl.iteye.com/upload/attachment/494348/bce5d87a-58a4-3373-9f7f-cab4ec2aaa85.jpg)

像FileInputStream这样的处理器叫做流处理器，它就像流的管道一样，从一个流源吸入某种类型的数据，并输出某种类型的数据。上面这种示意图叫做流的管道图。
　同样道理，也可以用FileOutputStream类向一个磁盘文件写数据，如下图所示：
![](http://dl.iteye.com/upload/attachment/494349/b210beef-7693-3ceb-bd16-34535419a6f3.jpg)

在实际应用这种机制并不没有太大的用处，程序需要写出地通常是非常结构化的信息，因此这些byte类型的数据实际上是一些数值，文字，源代码等。Java的I/O库提供了一个称做链接（Chaining）的机制，可以将一个流处理器跟另一个流处理器首尾相接，以其中之一的输出为输入，形成一个流管道的链接。
　例如，DataInputStream流处理器可以把FileInputStream流对象的输出当作输入，将Byte类型的数据转换成Java的原始类型和String类型的数据。如下图所示：
![](http://dl.iteye.com/upload/attachment/494352/0a1807fd-4d9f-349a-8c60-7277113b117a.jpg)

　类似地，向一个文件写入Byte类型的数据不是一个简单的过程。一个程序需要向一个文件里写入的数据往往都是结构化的，而Byte类型则是原始类型。因此在写的时候必须经过转换。DataOutputStream流处理器提供了接收了原始数据类型和String数据类型，而这个流处理器的输出数据则是Byte类型。也就是说DataOutputStream可以将源数据转换成Byte类型的数据，再输出来。
　这样一来，就可以将DataOutputStream与FileOutputStream链接起来，这样程序就可以将原始数据类型和String类型的源数据写入这个链接好的双重管道里面，达到将结构化数据写到磁盘文件里面的目的，如下图所示：
![](http://dl.iteye.com/upload/attachment/494354/99bfea32-c9bd-3cbf-8ed0-c22f365ed460.jpg)

　这又是链接的所发挥的大作用。
　流处理器所处理的流必定都有流源，而如果将流类所处理的流源分类的话，基本可以分成两大类：
　第一　数组，String，File等，这一种叫原始流源。
　第二　同样类型的流用做链接流类的流源，叫链接流源。
### 二　Java I/O库的设计原则

Java语言的I/O库是对各种常见的流源，流汇以及处理过程的抽象化。客户端的Java程序不必知道最终的流源，流汇是磁盘上的文件还是数组等；也不必关心数据是否经过缓冲的，可否按照行号读取等处理的细节。
　书中提到了，对于第一次见到Java/IO库的人，无不因为这个库的庞杂而感到困惑；而对于熟悉这个库的人，而又常常为这个库的设计是否得当而争论不体。书的作者提出自己的意见，要理解Java I/O这个庞大而复杂的库，关键是要掌握两个对称性跟两个设计模式模式。
 
Java I/O库具有两个对称性，它们分别是：

　1.　输入－输出对称性，比如InputStream和OutputStream各自占据Byte流的输入与输出的两个平行的等级结构的根部。而Reader和Writer各自占据Char流的输入与输出的两个平行的等级结构的根部。
　
  2.　byte-char对称，InputStream和Reader的子类分别负责Byte和Char流的输入；OutputStream和Writer的子类分别负责Byte和Char流的输出，它们分别形成平行的等级结构。
 
#### Java I/O库的两个设计模式：
Java的I/O库总体设计是符合装饰者模式（Decorator）跟适配器模式（Adapter）的。如前所述，这个库中处理流的类叫做流类。引子里所谈到的FileInputStream，FileOutputStream，DataInputStream及DataOutputStream都是流处理器的例子。
 
1. 装饰者模式：在由InputStream，OutputStream,Reader和Writer代表的等级结构内部，有一些流处理器可以对另一些流处理器起到装饰作用，形成新的，具有改善了的功能的流处理器。装饰者模式是Java I/O库的整体设计模式。这样的一个原则是符合装饰者模式的，如下图所示：
 ![](http://dl.iteye.com/upload/attachment/494355/0a5cf748-5e49-3fcb-a133-2a6931b99ba2.jpg)

2. 适配器模式：在由InputStream，OutputStream,Reader和Writer代表的等级结构内部，有一些流处理器是对其它类型的流源的适配。这就是适配器模式的应用，如下图所示。

![](http://dl.iteye.com/upload/attachment/494362/1861d69a-e084-3e52-8236-e80514d4ae53.jpg)

### 三.装饰模式的应用

学过装饰模式后，大家会发现，它在Java语言中最著名的应用莫过于Java I/O标准为库的设计了。这一节将以处理Byte流为例，看看装饰模式是怎样得到应用的。
 
　为什么不用继承而用装饰模式
　我们知道Java I/O库需要很多性能的各种组合，如果说这些性能的组合是通过继承方式来实现的话，那么每一种组合都需要一个类，这样就会出现大量重复性问题的出现，从而使类数目“爆炸”。而如果采用装饰模式，那么不仅类的数目大减少了，性能的重复也可以减至到最少。所以装饰模式是Java I/O库的基本模式。在这里我想再用<<Head First Design Pattern>>中讲到装饰模式时候的一个例子，看看装饰模式是怎么达到不仅类的数目大减少了，性能的重复也可以减至到最少。
　再回到Java I/O库，由于装饰模式的引用，造成了灵活性和复杂都大大增加了，我们在使用Java I/O库时，必须理解Java I/O库是由一些基本的原始流处理器和围绕它们的装饰流处理器所组成的，这样可以在学习和使用Java I/O库时达到事半功倍的效果。
　下面我用<<Java与模式>>，<<Head First Design Pattern>>或者是网上看到的一些类图来分析：
 
　首先是InputStream类型中的装饰模式：
　InputStream有七个直接的具体子类，有四个属于FilterInputStream的具体子类，如下图所示：

![](http://dl.iteye.com/upload/attachment/494364/799283eb-241b-3816-bc09-94c98077f673.jpg)

上图中所有的类都叫做流处理器，这个图就叫做（InputStream类型的）流处理器图。
　　书中提到根据输入流的源的类型，可以将这些流类分成两种，即原始流类（Original Stream）和链接流处理器（Wrapper Stream）。

#### 原始流处理器
　　原始流处理器接收一个Byte数组对象，String对象，FileDiscriptor对象或者不同类型的流源对象，根据上面的图，原始流处理器包括以下四种：
　　ByteArrayInputStream：为多线程的通信提供缓冲区操作功能，接收一个Byte数组作为流的源。
　　FileInputStream:建立一个与文件有关的输入流。接收一个File对象作为流的源。
　　PipedInputStream：可以与PipedOutputStream配合使用，用于读入一个数据管道的数据，接收一个PipedOutputStream作为源。
　　StringBufferInputStream：将一个字符串缓冲区转换为一个输入流。接收一个String对象作为流的源。（ＪＤＫ帮助文档上说明：已过时。此类未能正确地将字符转换为字节。从ＪＤＫ1.1开始，从字符串创建流的首选方法是通过StringReader类进行创建。只有字符串中每个字符的低八位可以由此类使用。）
 
#### 链接流处理器
　　所谓链接流处理器，就是可以接收另一个流对象作为源，并对之进行功能扩展的类。InputStream类型的链接处理器包括以下几种，它们都接收另一个InputStream对象作为流源。
1. FilterInputStream称为过滤输入流，它将另一个输入流作为流源。这个类的子类包括以下几种：

- BufferedInputStream：用来从硬盘将数据读入到一个内存缓冲区中，并从缓冲区提供数据。
- DataInputStream：提供基于多字节的读取方法，可以读取原始类型的数据。
- LineNumberInputStream：提供带有行计数功能的过滤输入流。
- PushbackInputStream：提供特殊的功能，可以将已经读取的字节“推回”到输入流中。
2. ObjectInputStream可以将使用ObjectInputStream串行化的原始数据类型和对象重新并行化。
3. SeqcueneInputStream可以将两个已有的输入流连接起来，形成一个输入流，从而将多个输入流排列构成一个输入流序列。
　　抽象结构图
　　按照上面的这种原始流处理器和链接流处理器的划分，可以用下面的结构图来描述它们之间的关系。
　　![](http://dl.iteye.com/upload/attachment/494366/85abdba8-14a2-3d37-a0e1-8ddfbfb765d5.jpg)

　上面的流处理器图跟装饰模式的结构图有着显而易见的相同之处。实际上InputStream类型的流处理器结构确实符合装饰模式。　
　装饰模式结构图

![](http://dl.iteye.com/upload/attachment/494374/941a1040-662d-3b9d-9221-984898117797.jpg)

对于上图FilterInputStream查看JDK1.6源代码，部分代码如下:
``` java
public class FilterInputStream extends InputStream {  
    protected volatile InputStream in;   
  
    protected FilterInputStream(InputStream in) {  
       this.in = in;  
    }  
    //其它代码  
}  
```


 
FilterInputStream继承了InputStream,也引用了InputStream,而它有四个子类,这就是所谓的Decorator模式。
上面这个图向我们传达了这个信息：链接流对象接收一个原始流对象或者另外一个链接流对象作为流源；另一方面他们对流源的内部工作方法做了相应的改变，这种改变是装饰模式所要达到的目的。比如：


- BufferedInputStream“装饰”了InputStream的内部工作方式，使得流的读入操作使用了缓冲机制。在使用了缓冲机制后，不会对每一次的流读入操作都产生一个物理的读盘动作，从而提高了程序的效率，在汲及到物理流的读入时，都应当使用这个装饰流类。
- LineNumberInputStream和PushbackInputStream也同样“装饰”了InputStream的内部工作方式，前者使得程序能够按照行号读入数据；后者能够使程序读入的过程中，退后一个字符。
- DataInputStream子类读入各种不同的原始数据类型以及String类型的数据，这一点可以从它提供的各种read方法看出来，如：readByte(),readInt(),readFloat()等。
　 Java语言的I/O库提供了四大等级结构:InputStream,OutputStream,Reader,Writer四个系列的类。InputStream和OutputStream处理8位字节流数据, Reader和Writer处理16位的字符流数据。InputStream和Reader处理输入, OutputStream和Writer处理输出，所以OutputStream,Reader,Writer这三类的装饰模式跟前面详细介绍的InputStream装饰模式大同小异，大家可以看书中其它部分对这三类的详细描述或者从网上也能找到有关资料。为了方便比较这几种类型，顺便附上Java语言的I/O层次结构图：
　下面的图表示:以InputStream和OutputStream形成的层次关系

![](http://dl.iteye.com/upload/attachment/494370/c7efcc56-39b7-39d8-b815-7c448d25f474.jpg)

下面的图表示:以Reader和Writer形成的层次关系

![](http://dl.iteye.com/upload/attachment/494379/d2f59584-a278-3707-93b0-bb91cd7ea082.jpg)

### 四　适配器模式的应用 ###

适配器模式是Java I/O库中第二个最为重要的设计模式。
InputStream原始流处理器中的适配器模式，InputStream类型的原始流处理器是适配器模式的应用。
ByteArrayInputStream是一个适配器类
　ByteArrayInputStream继承了InputStream的接口，而封装了一个byte数组。换言之，它将一个byte数组的接口适配成InputStream流处理器的接口。
　我们知道Java语言支持四种类型：Java接口，Java类，Java数组，原始类型（即int,float等）。前三种是引用类型，类和数组的实例是对象，原始类型的值不是对象。
也即，Java语言的数组是像所有的其他对象一样的对象，而不管数组中所存储的元素类型是什么。
这样一来的话，ByteArrayInputStream就符合适配器模式的描述，是一个对象形式的适配器类。
 
FileInputStream是一个适配器类
在FileInputStream继承了InputStrem类型，同时持有一个对FileDiscriptor的引用。这是将一个FileDiscriptor对象适配成InputStrem类型的对象形式的适配器模式。
查看JDK1.6的源代码我们可以看到：


```java
public  class FileInputStream extends InputStream  
{  
    private FileDescriptor fd;  
  
    private FileChannel channel = null;  
  
 public FileInputStream(File file) throws FileNotFoundException {  
      String name = (file != null ? file.getPath() : null);  
      SecurityManager security = System.getSecurityManager();  
      if (security != null) {  
        security.checkRead(name);  
       }  
        if (name == null) {  
            throw new NullPointerException();  
        }  
       fd = new FileDescriptor();  
       open(name);  
    }  
  
   //其它代码  
}  

```

- StringBufferInputStream继承了InputStream类型，同时持有一个对String对象的引用，这是一个将String对象适配成InputStream类型的对象形式的适配器模式。
- OutputStream原始流处理器中的适配器模式，同样地，在OutputStream类型中，所有的原始流处理器都是适配器类。
- ByteArrayOutputStream继承了OutputStream类型，同时持有一个对byte数组的引用。它一个byte数组的接口适配成OutputString类型的接口，因此也是一个对象形式的适配器模式的应用。
 
- FileOutputStream是一个适配器类
FileOutputStream继承了OutputStream类型，同时持有一个对FileDiscriptor对象的引用。这是一个将FileDiscriptor接口适配成OutputStream接口形式的对象形适配器模式。
Reader原始流处理器中的适配器模式，Reader类型的原始流处理器都是适配器模式的应用。
 
- StringReader是一个适配器类
StringReader类继承了Reader类型，持有一个对String对象的引用。它将String的接口适 配成Reader类型的接口。


**从byte流到char流的适配**

在Java I/O库中，使用比较频繁的要数InputStreamReader,OutputStreamWriter这两种类了，
InputStreamReader是从byte输入流到char输入流的一个适配器。
当把InputStreamReader与任何InputStream的具体子类链接的时候，可以从InputStream的输出读入byte类型的数据，将之转换成为char类型的数据，如下图所示：

![](http://dl.iteye.com/upload/attachment/494383/102de2be-2e6d-313e-b1ca-3c6c0e3ebf17.jpg)

``` java

public class InputStreamReader extends Reader {  
  
    private final StreamDecoder sd;  
  
    public InputStreamReader(InputStream in) {  
       super(in);  
        try {  
           sd = StreamDecoder.forInputStreamReader(in, this, (String)null);   
          // ## check lock object  
        } catch (UnsupportedEncodingException e) {  
     // The default encoding should always be available  
     throw new Error(e);  
 }  
  
 //其它代码  
}  

```

其中StreamDecoder是sun.nio.cs这个包里的一个类OutputStreamWriter是适配器类。



同样道理我们能得出OutputStringWriter是从OutputStream到Writer的适配器类。也就是说，与任何一个OutputStream的具体子类相链接时，OutputStringWriter可以将OutputStream类型的byte流适配成为char流。它的源代码跟上面的InputStreamReader差不多，这就不贴出来，感兴趣可以查看JDK1.4在线源码这本书后面还有个小例子，附有一些讲解，我就不列出来了，有书的可以看看。
 
### 五 总结
在这三篇文章里主要是汲及到三个知识点：知识点一: Java I/O库的四大等级结构
Java语言的I/O库提供了四大等级结构:InputStream,OutputStream,Reader,Writer四个系列的类。InputStream和OutputStream处理8位字节流数据, Reader和Writer处理16位的字符流数据。InputStream和Reader处理输入, OutputStream和Writer处理输出。
知识点二: Decorator模式在Java I/O库的应用
知识点三: Adapter模式在Java I/O库的应用
