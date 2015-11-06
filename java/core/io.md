#### java io体系 ####  
![]()  
如图可以看出，java的io按照包来划分的话可以分为三大块：io、nio、aio，但是从使用角度来看，这三块其实揉杂在一起的，下边我们先来概述下这三块：  
* io:主要包含字符流和字节流，我们常用的文件读写，流处理等都要用到，也是本次介绍的重点。**jdk1.7之后的io底层部分类经已改为使用阻塞的nio实现了**
* nio：jdk1.4后加入，多路非阻塞(多路IO复用模型)，此外还实现了buffer、channel、selector、内存映射文件等实现。我们直接使用nio多数情况用于网路编程。
* aio：jdk1.7支持，又叫做nio2，实现了异步非阻塞io。较nio更高效，也主要用于网络编程。  
由于本次主要介绍java基本的io类结构，io类的实现相对简单（主要是字节或者字符数组的操作），而nio和aio更多时候我们关注的是网络编程，要想理解需要对unix网络模型、异步和阻塞等有比较清晰的理解，因此并不会花大量篇幅介绍各个类，示例也仅会给出链接。
快写完的时候看到的一篇，写的赞，果然水平还差的远：[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/)

#### io类结构 ####  
![]()  
常用的类不在介绍，下边主要介绍一些有特殊功能的类

##### PipedWriter，PipedReader #####  
管道输入流，熟悉linux系统应该看到pipe应该就明白了，可以实现线程间通信，需要和PipedReader配套使用。[详解和示例](http://www.2cto.com/kf/201312/263319.html )

##### FilterReader，FilterWriter #####  
根据名字也能看出来，目的事项实现一个流过滤器，但是你会发现其实是个抽象类，并没有实现，这里只是提供了接口。扫了一下发现jdk里边只有Utility的一个JavaWriter的静态类实现了，其功能是将字节转换成有效的java字符。


##### CharArrayReader #####      
http://www.2cto.com/kf/201312/263329.html

## PushbackReader  
http://m.blog.csdn.net/blog/u013087513/25916007 

## DataInputStream  
http://blog.csdn.net/baple/article/details/38494663

## SequenceInputStream  
http://blog.csdn.net/xuefeng1009/article/details/6955707 

## ByteArrayInputStream    
http://blog.csdn.net/rcoder/article/details/6118313 

## RandomAccessFile  
http://blog.csdn.net/akon_vm/article/details/7429245 


https://www.ibm.com/developerworks/cn/java/j-lo-javaio/
http://www.cnblogs.com/kuangdaoyizhimei/p/4034232.html
http://www.cnblogs.com/kuangdaoyizhimei/p/4035611.html