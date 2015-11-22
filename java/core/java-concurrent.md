### 概述 ###
并发处理本身就是编程开发重点之一，同时内容也很繁杂，从底层指令处理到上层应用开发都要涉及，也是最容易出问题的地方。这块知识也是评价一个开发人员水平的重要指标，本人自认为现在也只是学其皮毛，因此本文重点介绍java的并发相关体系，具体的点懂得就多讲，不懂得就给出参考文章。先来看图：
![]()  
本文重点介绍jdk中concurrent的内容，并发相关基础不在介绍，如果想系统的学习，建议直接看并发大作《java并发编程实践》吧(之前看过，可惜很多地方没懂)，博客终究只是快餐，增强学习拓展知识很好，但打基础还是要看书实战的。  
### java内存模型 ###
直接盗图一张，详细讲解参考[博客](http://www.infoq.com/cn/articles/java-memory-model-1)。  
![](http://img.my.csdn.net/uploads/201302/06/1360141335_1299.png)  
java内存模型的作用可以理解为抽象了线程私有内存与主存（共享内存或堆）的关系，也就是原子性、可见性、顺序性的原则，而后介绍的内容都是为了保证这些原则的实现手段。  

### 实现多线程的方法 ###
这一块其实不必多说，大家都很熟悉，这里简单对比下优缺点：  
* 继承Thread：由于java不支持多继承，所以用起来有很强的局限性，使用场景不多。
* 实现Runnable接口：常用的方式，有点对比Thread，并且更方便实现资源共享（[两者异同](http://mars914.iteye.com/blog/1508429),重点在评论）
* 实现Callable接口: 结合Future使用，可以获取线程执行结果
* Executor：concurrent中提供的一个上层并发处理框架，底层也是基于上边三种实现，基于此实现了线程池、任务调度等类，为一些典型场景提供了便捷的实现手段。

### 实现同步的方法 ###
这一块也是常用的，也仅对比介绍一下：  
* volatile：volatile保证了原子操作在线程间的可见性(注意仅能保证可见性)，并且修饰对象的操作不会指令重排。对于一个原子操作可以保证其之间一致，但是原子操作真的很少，比如i++都不行。[更多介绍](http://www.cnblogs.com/dolphin0520/p/3920373.html)
* Atomic：原子类，，基于[CAS](http://blog.csdn.net/hsuxu/article/details/9467651)原理实现，提供了基本类型和引用对应的类，能保证其基本操作的可见性，底层基于volatile和Unsafe类（一个线程安全相关类，可以调用底层native方法）。如果线程间的同步仅限于某个值的改变，则可考虑用该类（实际上多数时候即使适合用也能找到对应的上层封装类，而不必自己实现）
* synchronized：最为常用的同步方法之一，可以分为同步方法和同步代码块两类，使用简单，唯一一定要搞清的是synchronized锁的对象是谁。（拓展：[synchronized底层实现](http://www.sxt.cn/u/756/blog/2624)）
* wait/notify：同步几大原语之二（记得还有join吧），java的Object中已实现的native方法，常用来同步线程执行顺序或进度，然而多数场景concurrent也提供了对应工具类，所以一般使用时也应该优先使用对应的工具类
* Lock：锁，主要有ReentrantLock和ReadWriteLock，该方式较synchronized的优势就是使用比较灵活，同步不再局限于代码块或者方法，可以在任何需要的地方加锁解锁。就性能方面，除非你用的是1.4之前的jdk，否则两者差异不大
* ThreadLocal：线程本地变量，其内部是一个map，key为线程对象本身，value为对应变量的一个拷贝，每个线程使用该变量时实际使用的是其副本，以此解决多线程共享变量的竞争，**需要注意的是改类型并不是解决同步问题的，而是解决资源共享问题的，每个线程使用各自的副本，相互之间不影响，但是该变量的值变化相互之间也是隔离的**[ThreadLocal深入剖析](http://www.cnblogs.com/dolphin0520/p/3920407.html)

### concurrent包 ###
JDK 1.5增加了java.util.concurrent包，其内部提供了大量并发相关类，大大简化了设计并发的程序开发。该包大致可分为四大块：Atomic、Lock、Executor、以及线程安全的集合类，由于集合类已经在该系列第一篇介绍过，因此这里重点介绍前三块，此外还有一些工具类，这里仅介绍几个常见的。

#### Atomic ####
前边已经介绍过，Atomic为修饰的对象提供了原子更新，保证了其更新在线程间的可见性，Atomic包内的原子类实现主要基于CAS原理，利用了Unsafe包提供的CAS方法，其中可以分为以下几类：  
* 原子更新基本类型类，提供了AtomicBoolean、AtomicInteger和AtomicLong，对于其他基本类型，可以参照其实现自行通过Unsafe的方法实现（实质上都是转成int处理）
* 原子更新数组类：也提供了三种类型：AtomicIntegerArray、AtomicLongArray和AtomicReferenceArray
* 原子更新引用类型：对于非基本类型提供的类，AtomicReference、AtomicReferenceFieldUpdater、AtomicMarkableReference
* 原子更新字段类：这几个类主要用于更新类中的某个字段：AtomicIntegerFieldUpdater、AtomicLongFieldUpdater、AtomicStampedReference  
这些类使用方法类似，都是通过以下方法实现原子更新：  
* getAndSet(v)：设置新值，返回旧值
* compareAndSet(expectedValue, newValue)：如果当前值(current value)等于期待的值(expectedValue), 则原子地更新指定值为新值(newValue), 如果更新成功，返回true, 否则返回false, 换句话可以这样说: 将原子变量设置为新的值, 但是如果从我上次看到的这个变量之后到现在被其他线程修改了(和我期望看到的值不符), 那么更新失败  
详细的使用可以参考[Java中的Atomic包使用指南](http://ifeve.com/java-atomic/)

#### Lock ####
Lock提供了一种更为灵活的同步方式，Lock下主要有以下类：  
* ReentrantLock：重入锁，其底层通过一个Sync的静态类实现加锁解锁。Sync继承自AbstractQueuedSynchronizer，该抽象类内部实现了一个链表，保存了请求获取锁的状态，同时其内部有一个状态值，用来表示锁是否被线程获取，其修改通过Unsafe包提供的CAS方法，以此可以保证其可见性。同时尤其内部实现也可以知道，之所以叫重入锁，是因为其提供了非阻塞的使用方式，如果使用tryLock方法，当lock时在发现锁已lock的情况下，会立即返回结果，而不会阻塞。[使用示例](http://my.oschina.net/noahxiao/blog/101558)
* ReentrantReadWriteLock：可重入读写锁。实现原理和ReentrantLock一样，再次基础上增加了读写锁的操作。[使用示例](http://www.cnblogs.com/liuling/archive/2013/08/21/2013-8-21-03.html)  
* Condition：实现类是AbstractQueuedSynchronizer中的一个内部类，其功能是实现线程间的协调通信，使得某个，或者某些线程一起等待某个条件（Condition），只有当该条件具备( signal 或者 signalAll方法被带调用)时 ，这些等待线程才会被唤醒，从而重新争夺锁。[使用示例](http://blog.csdn.net/ghsau/article/details/7481142)
* LockSupport：可以看做对Unsafe包中并发原语的封装，为上层的锁实现提供原语操作。一般情况下上层开发很少用到。  

#### Executor ####
Executor框架是concurrent中最常用的内容之一，其提供了一套能够便捷管理线程和任务的类，使我们能够很方便创建、调度一批具有同类操作的线程。其主要有以下几个类(接口)：  
* Callable、Future：前边已经介绍过了，提供了一种可以获取返回值的线程实现方法，一般与
ExecutorService配合使用。[Callable使用](http://www.cnblogs.com/dolphin0520/p/3949310.html)  
* Executor：线程工具类，主要用于线程池、ThreadFactory、Callable实例。[Executors详解](http://xiyuan1025.iteye.com/blog/1912639)
* ThreadFactory：接口类，提供了创建线程个工厂类，多是时候配合线程池，作为参数传入为线程池提供创建线程的方法。
* ExecutorService：接口类，是线程池实现类ThreadPoolExecutor的基类。
* ExecutorCompletionService：与ExecutorService功能一样，不过其提供了poll()和take()两个方法用于获取线程执行结果，前者是非阻塞的，后者是阻塞的。  
[使用示例](java并发编程-Executor框架)

#### other ####
此外concurrent包还提供了一些其他类，这里仅列出功能，具体的使用可自行查询，这里给出一个比较完善的总结[java.util.concurrent 用户指南](http://blog.csdn.net/defonds/article/details/44021605)  
* ForkJoinPool：和ExecutorService类似，不过其提供了fork和join的功能，能够将池内指定级别的任务进行分解或合并。这种处理方式与目前很多流处理框架类似，不过该实现使用的不多。
* CountDownLatch：CountDownLatch 是一个线程协调器，它允许一个或多个线程等待一系列指定操作的完成。
CountDownLatch 以一个给定的数量初始化。countDown() 每被调用一次，这一数量就减一。通过调用await() 方法之一，线程可以阻塞等待这一数量到达零。
* CyclicBarrier：CyclicBarrier类也是一种同步机制，它可以在指定位置设定barrier，只有所有线程都执行到该位置是，才会继续向下执行。
* Exchanger：该类提供了线程间交换数据的方法，可以视作一个管道。[实现原理](http://m.blog.csdn.net/blog/luoyuyou/30257073)
* Semaphore：信号量，学过操作系统的都知道，实现生产者—消费者的常用手段之一，信号量最大的用处是可以控制资源的访问数量，比起阻塞队列更加灵活。[使用示例](http://www.cnblogs.com/whgw/archive/2011/09/29/2195555.html)

### 其他 ###
正如最开始所说的，并发相关的知识绝不是看一些知识点就可以掌握的，如果想要真正掌握，必须系统的学习。这里只是列出一些java相关的知识点，仅供参考。  
最后再附两篇博文，有兴趣的可以看下：  
* http://www.importnew.com/14506.html
* http://icyfenix.iteye.com/blog/1018932
