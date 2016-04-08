title: java动态绑定内幕
date: 2015-07-29 15:18:45
tags: java
---



![java对象内存模型](http://hi.csdn.net/attachment/201005/9/0_1273390910MR3c.gif)
<!--more -->
``` java
//被调用的类 
package hr.test;
class Father{
      public static void f1(){
              System.out.println("Father— f1()");
      }
}
//调用静态方法
import hr.test.Father;
public class StaticCall{
       public static void main(){
            Father.f1(); //调用静态方法
       }
}
```

上面的源代码中执行方法调用的语句(Father.f1())被编译器编译成了一条指令：invokestatic #13。我们看看JVM是如何处理这条指令的

(1) 指令中的#13指的是StaticCall类的常量池中第13个常量表的索引项(关于常量池详见《Class文件内容及常量池 》)。这个常量表(CONSTATN_Methodref_info ) 记录的是方法f1信息的符号引用(包括f1所在的类名，方法名和返回类型)。JVM会首先根据这个符号引用找到方法f1所在的类的全限定名: hr.test.Father。
     

(2) 紧接着JVM会加载、链接和初始化Father类。
     

(3) 然后在Father类所在的方法区中找到f1()方法的直接地址，并将这个直接地址记录到StaticCall类的常量池索引为13的常量表中。这个过程叫常量池解析 ，以后再次调用Father.f1()时，将直接找到f1方法的字节码。
     

(4) 完成了StaticCall类常量池索引项13的常量表的解析之后，JVM就可以调用f1()方法，并开始解释执行f1()方法中的指令了。
 
 通过上面的过程，我们发现经过常量池解析之后，JVM就能够确定要调用的f1()方法具体在内存的什么位置上了。实际上，这个信息在编译阶段就已经在StaticCall类的常量池中记录了下来。这种在编译阶段就能够确定调用哪个方法的方式，我们叫做 静态绑定机制 。
 
 除了被static 修饰的静态方法，所有被private 修饰的私有方法、被final 修饰的禁止子类覆盖的方法都会被编译成invokestatic指令。另外所有类的初始化方法<init>和<clinit>会被编译成invokespecial指令。JVM会采用静态绑定机制来顺利的调用这些方法。


``` java
//被调用的父类
class Father{
	
	public void f1(){

		System.out.println("father-f1()");
	
	}
      
  public void f1(int i){
    
            System.out.println("father-f1()  para-int "+i);
        
	}

}

//被调用的子类

class Son extends Father{
	
	public void f1(){ 
	//覆盖父类的方法
		
	System.out.println("Son-f1()");
	
	}
        

}



public class AutoCall{

	public static void main(String[] args){
	
		Father father=new Son(); //多态
		
		father.f1(); //打印结果： Son-f1()
	
	}

}

```

## 动态绑定



上面的源代码中有三个重要的概念：多态(polymorphism) 、方法覆盖 、方法重载 。打印的结果大家也都比较清楚，但是JVM是如何知道f.f1()调用的是子类Sun中方法而不是Father中的方法呢？在解释这个问题之前，我们首先简单的讲下JVM管理的一个非常重要的数据结构——方法表 。
 
 >在JVM加载类的同时，会在方法区中为这个类存放很多信息(详见《Java 虚拟机体系结构 》)。其中就有一个数据结构叫方法表。它以数组的形式记录了当前类及其所有超类的可见方法字节码在内存中的直接地址 。下图是上面源代码中Father和Sun类在方法区中的方法表：


![](http://hxraid.iteye.com/upload/picture/pic/65417/ab4e5838-83f6-34a4-9dfc-a1b93e1ff02c.jpg)

上图中的方法表有两个特点：

(1) 子类方法表中继承了父类的方法，比如Father extends Object。

(2) 相同的方法(相同的方法签名：方法名和参数列表)在所有类的方法表中的索引相同。比如Father方法表中的f1()和Son方法表中的f1()都位于各自方法表的第11项中。

通过javap得到：

![](http://ww4.sinaimg.cn/large/b00f9334jw1eujpjiqr5rj20kl0bcta4.jpg)
![](http://ww1.sinaimg.cn/large/b00f9334jw1eujpjx65b1j20kb0cg3zo.jpg)

其中invokevirtual指令的详细调用过程是这样的：
 (1) invokevirtual指令中的#15指的是AutoCall类的常量池中第15个常量表的索引项。这个常量表(CONSTATN_Methodref_info ) 记录的是方法f1信息的符号引用(包括f1所在的类名，方法名和返回类型)。JVM会首先根据这个符号引用找到调用方法f1的类的全限定名: hr.test.Father。这是因为调用方法f1的类的对象father声明为Father类型。


(2) 在Father类型的方法表中查找方法f1，如果找到，则将方法f1在方法表中的索引项11(如上图)记录到AutoCall类的常量池中第15个常量表中(常量池解析 )。这里有一点要注意：如果Father类型方法表中没有方法f1，那么即使Son类型中方法表有，编译的时候也通过不了。因为调用方法f1的类的对象father的声明为Father类型。


(3) 在调用invokevirtual指令前有一个aload_1指令，它会将开始创建在堆中的Son对象的引用压入操作数栈。然后invokevirtual指令会根据这个Son对象的引用首先找到堆中的Son对象，然后进一步找到Son对象所属类型的方法表。

过程如下图所示：

![](http://ww4.sinaimg.cn/large/b00f9334jw1eujpud3s63j20ci0a8wfn.jpg)

(4) 这是通过第(2)步中解析完成的#15常量表中的方法表的索引项11，可以定位到Son类型方法表中的方法f1()，然后通过直接地址找到该方法字节码所在的内存空间。
 
>很明显，根据对象(father)的声明类型(Father)还不能够确定调用方法f1的位置，必须根据father在堆中实际创建的对象类型Son来确定f1方法所在的位置。这种在程序运行过程中，通过动态创建的对象的方法表来定位方法的方式，我们叫做 `动态绑定机制` 。