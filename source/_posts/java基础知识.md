title: java基础知识
date: 2015-07-12 15:47:00
tags: java
---


Java与C/C++不同点：

 >
 1. Java为解释型语言  程序经java编译器编译成字节码，然后在JVM上解释执行，C/C++则是经过编译和链接后生成可执行的二进制文件。所以C/C++一般比JAVA快，但不具备平台无关性。
 2. Java不支持多继承，无指针，无goto但保留goto关键字

public static void main(String[] args)为程序的入口地址，参数args为开发人员在命令行状态下与程序交互提供了一种手段

Java程序初始化过程：

（父类静态变量 |父类静态代码块） （子类静态变量 |子类静态代码块） 父类非静态变量 父类非静态代码块 父类构造函数 子类非静态变量  子类非静态代码块 子类构造函数
<!-- more-->
###clone()方法

![](http://img.blog.csdn.net/20140116224712140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhbmdqZ19ibG9n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

java的clone是浅复制，完成深复制需要对对象中的引用对象调用clone()方法

深复制和浅复制返回的都是不同于之前对象的引用，但是浅复制中 复制的对象内的引用对象还是指向之前的，深复制是独立出来的。
---
###三种方法获得class对象

1. class.forName()
2. 类名.class(不引起类加载)
3. 实例.getclass()


子类可以对父类中的方法进行访问权限的扩展 而不能缩小。
例如，基类方法是protected的 子类只能是Public protected 不能是private

###volatile关键字

系统每次用他的时候每次都去内存找，不用缓存，保证一致性。保证所有的线程看到的变量值是一样的。

###strictft

strictft是`strict float point`的缩写，指的是精确浮点，用来保证浮点运算的精确，可用阿里声明一个类、接口、方法


String s = new String("abc");创建了几个对象

>如果常量池中存在"abc"则只有一个对象 new String() 对象里的value指向常量池中的abc，如果不存在则先在常量池中构造一个对象，然后指向它，所以答案是一个或两个。

数组可以看成一个对象,int[] a  a instanceOf int[] 返回true

###java异常

java分为两种异常 Error 和Exception 拥有共同的父类 Throwable 

Error  

>java.lang.Error 用来处理系统错误，例如java.lang.StackOverFlowError 或者 Java.lang.OutOfMemoryError 之类的。然后 Exception用来处理程序错误，请求的资源不可用等等

Exception分为 检查异常 和运行时异常

>检查型异常和非检查型异常的主要区别在于其处理方式。检查型异常需要使用try, catch和finally关键字在编译期进行处理，否则编译器会报错。对于非检查型异常则不需要这样做。Java中所有继承自java.lang.Exception类的异常都是检查型异常，所有继承自RuntimeException的异常都被称为非检查型异常。你也可以查看下一篇文章来了解 更多关于检查型异常和非检查型异常之间的区别.












 