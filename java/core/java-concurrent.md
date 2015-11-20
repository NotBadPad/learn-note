### 概述 ###
并发处理本身就是编程开发重点之一，同时内容也很繁杂，从底层指令处理到上层应用开发都要涉及，也是最容易出问题的地方。这块知识也是评价一个开发人员水平的重要指标，本人自认为现在也只是学其皮毛，因此本文重点介绍java的并发相关体系，具体的点懂得就多讲，不懂得就给出参考文章。先来看图：
![]()  
本文重点介绍jdk中concurrent的内容，并发相关基础不在介绍，如果想系统的学习，建议直接看并发大作《java并发编程实践》吧(之前看过，可惜很多地方没懂)，博客终究只是快餐，增强学习拓展知识很好，但打基础还是要看书实战的。

#### java内存模型 ####  
直接盗图一张，详细讲解参考[博客](http://www.infoq.com/cn/articles/java-memory-model-1)。
![]()  
java内存模型的作用可以理解为抽象了线程私有内存与主存（共享内存或堆）的关系，也就是原子性、可见性、顺序性的原则，而后介绍的内容都是为了保证这些原则的实现手段。  

#### 实现多线程的方法 ####  
这一块其实不必多说，大家都很熟悉，这里简单对比下优缺点：  
* 继承Thread：由于java不支持多继承，所以用起来有很强的局限性，使用场景不多。
* 实现Runnable接口：常用的方式，有点对比Thread，并且更方便实现资源共享（[两者异同](http://mars914.iteye.com/blog/1508429),重点在评论）
* 实现Callable接口: 结合Future使用，可以获取线程执行结果
* Executor：concurrent中提供的一个上层并发处理框架，底层也是基于上边三种实现，基于此实现了线程池、任务调度等类，为一些典型场景提供了便捷的实现手段。

#### 实现同步的方法 ####  
这一块也是常用的，也仅对比介绍一下：  
* volatile：volatile保证了原子操作在线程间的可见性(注意仅能保证可见性)，并且修饰对象的操作不会指令重排。对于一个原子操作可以保证其之间一致，但是原子操作真的很少，比如i++都不行。
* Atomic：原子类，，基于[CAS](http://blog.csdn.net/hsuxu/article/details/9467651)原理实现，提供了基本类型和引用对应的类，能保证其基本操作的可见性，底层基于volatile和Unsafe类（一个线程安全相关类，可以调用底层native方法）。如果线程间的同步仅限于某个值的改变，则可考虑用该类（实际上多数时候即使适合用也能找到对应的上层封装类，而不必自己实现）
* synchronized：最为常用的同步方法之一，可以分为同步方法和同步代码块两类，使用简单，唯一一定要搞清的是synchronized锁的对象是谁。
* wait/notify：同步几大原语之二（记得还有join吧），java的Object中已实现的native方法，常用来同步线程执行顺序或进度，然而多数场景concurrent也提供了对应工具类，所以一般只有底层一些处理需要使用




内存
http://www.infoq.com/cn/articles/java-memory-model-1

cas
http://blog.csdn.net/hsuxu/article/details/9467651

volatile
http://www.cnblogs.com/dolphin0520/p/3920373.html

ThreadLoacl
http://www.cnblogs.com/dolphin0520/p/3920407.html

四种并发
http://www.importnew.com/14506.html

concurrent
http://blog.csdn.net/defonds/article/details/44021605

LockSupport
http://www.tuicool.com/articles/MveUNzF

轻量级锁
http://icyfenix.iteye.com/blog/1018932