#### java io体系 ####
![](https://raw.githubusercontent.com/NotBadPad/learn-note/master/other/20151106p1.png)  
如图可以看出，java的io按照包来划分的话可以分为三大块：io、nio、aio，但是从使用角度来看，这三块其实揉杂在一起的，下边我们先来概述下这三块：  
* io:主要包含字符流和字节流，我们常用的文件读写，流处理等都要用到，也是本次介绍的重点。**jdk1.7之后的io底层部分类经已改为使用阻塞的nio实现了**
* nio：jdk1.4后加入，多路非阻塞(多路IO复用模型)，此外还实现了buffer、channel、selector、内存映射文件等实现。我们直接使用nio多数情况用于网路编程。
* aio：jdk1.7支持，又叫做nio2，实现了异步非阻塞io。较nio更高效，也主要用于网络编程。  
由于本次主要介绍java基本的io类结构，io类的实现相对简单（主要是字节或者字符数组的操作），而nio和aio更多时候我们关注的是网络编程，要想理解需要对unix网络模型、异步和阻塞等有比较清晰的理解，因此并不会花大量篇幅介绍各个类，示例也仅会给出链接。  

快写完的时候看到的一篇，写的赞，果然水平还差的远：[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/)

#### io类结构 ####
![](https://raw.githubusercontent.com/NotBadPad/learn-note/master/other/20151208p2.png)  
常用的类不在介绍，下边主要介绍一些有特殊功能的类  

##### PipedWriter，PipedReader #####
管道输入流，熟悉linux系统应该看到pipe应该就明白了，可以实现线程间通信，需要和PipedReader配套使用。[详解和示例](http://www.2cto.com/kf/201312/263319.html )  

##### FilterReader，FilterWriter #####
根据名字也能看出来，目的事项实现一个流过滤器，但是你会发现其实是个抽象类，并没有实现，这里只是提供了接口。扫了一下发现jdk里边只有Utility的一个JavaWriter的静态类实现了，其功能是将字节转换成有效的java字符。  

##### CharArrayReader，CharArrayWriter #####
将字节数组作为输入或输出流处理，一般作为中间值，用来将字符数组和其他IO字符处理类转换。下边的ByteArrayInputStream与此类似，不过处理的是字节数组。[详解和示例](http://www.2cto.com/kf/201312/263329.html)  

##### PushbackReader，PushbackInputStream #####
具有回推功能的IO处理类，可以将已读取过的数据再回推到缓冲区中，重复读取(注意一般的流都是单向的，一旦读取出来就不能再读了)。[详解和示例](http://blog.csdn.net/ma451152002/article/details/11900917)  

##### DataInputStream，DataOutputStream #####
提供了按照类型或编码读取写入文件的方法，如byte[]、int、short、char、byte、UTF等类型或编码的读写。[详解和示例](http://blog.csdn.net/baple/article/details/38494663)  

##### SequenceInputStream #####
如其名字：顺序输入流，改类允许将多个输入流作为输入，并按照顺序处理多个流，使用的时候当做一个流处理。[详解和示例](http://blog.csdn.net/xuefeng1009/article/details/6955707)  

##### RandomAccessFile #####
该类与其他IO类有较大不同，其支持随机读写，对于格式化的记录文件读取很有优势。此外由于其底层的操作已经改为由nio的FileChannel实现，因此在处理大文件的时候经常使用RandomAccessFile和MappedByteBuffer来读取，不仅读取速度更快，而且能够避免文件过大导致内存溢出。[详解和示例](http://blog.csdn.net/akon_vm/article/details/7429245)  

##### 其他参考博文（侵删） #####
* https://www.ibm.com/developerworks/cn/java/j-lo-javaio/  
* http://www.cnblogs.com/kuangdaoyizhimei/p/4034232.html  
* http://www.cnblogs.com/kuangdaoyizhimei/p/4035611.html  

#### nio和aio相关 ####
![](https://raw.githubusercontent.com/NotBadPad/learn-note/master/other/20151107p1.png)  
上图是整理的nio与aio的一些关键点，瞟一眼就好，详细的nio讲解可参考： [Java NIO使用及原理分析](http://blog.csdn.net/wuxianglong/article/details/6604817)  