_（本文部分图片引用自其他博客，最后有链接，侵删。另今天先完成一部分占坑，明天补全）_ 
### 集合结构 ###
![](https://raw.githubusercontent.com/NotBadPad/learn-note/master/java/core/java-collection.png)  
红字为java.util包下的，绿字为concurrent包下扩展的与并发相关的类

### List ###
##### ArrayList #####
功能：有序非线程安全列表  
要点：  
* 底层存储用Object数组
* 元素可为null
* 自动扩容至1.5倍
* 适合随机访问，不适合大量或频繁增删

##### LinkedList #####
功能：有序非线程安全列表  
要点：  
* 基于双向链表实现
* 元素可为null
* 同时实现了双端队列Deque，支持队列、堆栈操作
* 适合增删，顺序访问，不适合随机访问

##### Vector #####
功能：有序线程安全列表  
要点：  
* 功能和ArrayList一样，只有线程安全区别，效率较ArrayList低
* 实际上算是老版本的List，除了线程安全的情况下不用

##### Stack #####
功能：基于Vector实现的堆栈  
要点：  
* 线程安全
* 后进先出（LIFO）1

##### CopyOnWriteArrayList #####
功能：快照版本的ArrayList，修改操作通过复制一份数组操作  
要点：  
* 线程安全
* 任何修改都会在拷贝的新副本上进行
* 修改操作通过ReentrantLock同步
* 迭代器只能访问，不能修改，且不会因为遍历过程中修改而抛异常

##### 实现线程安全List的方法 #####  
* 使用synchronized：对List操作是进行同步，需要自己控制
* 使用Vector：效率低，已经较少使用了
* 使用CopyOnWriteArrayList，前边已经介绍了，这个只能在量小读多写少的场景下用，且性能很差，一般不要用
* 使用Collections.synchronizedList()：工具类提供的一个比较简便的方法，可以直接创建一个线程安全的list，建议使用。其原理是对ArrayList进行包装，通过一个对象锁来控制并发，需要注意的是要弄清楚对象锁锁的是哪个对象。 

### Map ###
##### HashMap #####
功能：  
要点：key-value的map,存储位置根据key的hash值确定  
* 非线程安全
* 底层使用Entry<K, V>数组table
* 冲突使用链存储
* table长度为2^n，这样结合hash算法有利于均匀分布
* 当发生冲突时，新元素会插入到链首
* 每次table容量变化时，需要重新rehash
* 键值均可为null  
![](http://h.hiphotos.baidu.com/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=f197f18d087b020818c437b303b099b6/91ef76c6a7efce1b028711e2ad51f3deb48f656e.jpg)

##### HashTable #####
功能：key-value的map,存储位置根据key的hash值确定，与Hash的算法不同之处是，其位置直接通过hash(key)%table.length计算（HashMap使用h&(length – 1)计算，效率更高，才外还有一些细节不同，可自行参看源码）  
要点：  
* 线程安全
* 继承自Dictionary 
* 底层使用Entry<K, V>数组table
* 冲突使用链存储
* table长度为2^n，这样结合hash算法有利于均匀分布
* 当发生冲突时，新元素会插入到链首
* 每次table容量变化时，需要重新rehash
* 键值不能为空  
![](https://camo.githubusercontent.com/d3a88df46affd6adc7d37925a4ed99cee128da16/687474703a2f2f636d73626c6f67732e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031342f30342f315f7468756d622e706e67)

##### TreeMap #####
功能：基于[红黑树](http://www.cnblogs.com/yangecnu/p/Introduce-Red-Black-Tree.html)实现的有序集合 
要点：  
* 非线程安全
* 底层为一个棵红黑树
* 继承NavigableMap,主要实现了SortMap，因此是有序的
* 增删改查都可参考红黑树操作

##### ConcurrentHashMap #####
功能：线程安全的HashMap，属于java.util.concurrent下的拓展类  
要点：  
* 线程安全
* 底层实现是一个Segment<K, V>数组，Segment内部通过一个HashEntry<K, V>数组存储元素数据，同时Segment继承了[ReentrantLock](http://hyxw5890.iteye.com/blog/1578597)，直接通过本身的锁对改段数据操作同步
* 从底层结构可以发现，其修改时不会对整个集合加锁，而是对段加锁，因此允许同时修改多个段，效率更高
* 继承NavigableMap,主要实现了SortMap，因此是有序的
* Segment的划分是通过key的hashCode计算

##### ConcurrentSkipListMap #####
功能：线程安全的有序Map，是通过一个叫做[跳表](http://blog.sina.com.cn/s/blog_72995dcc01017w1t.html)的数据结构实现 
要点：  
* 线程安全
* 底层实现是一个多层链表，每层元素都有序，每低一层都包含上一层的元素，最底层包含所有元素。这样设计的巧妙之处是从上层逐层查找时，能够快速定位元素区间。思想上有点像折半查找，不过是把每次折半的中间值分层存了起来。同时链表结构是修改操作同样高效
* key是有序的
* 空间换时间，效率很高，但是空间占用也很厉害  
![](https://camo.githubusercontent.com/260a8e552f3790ade3c9f69c178658082ee3b6f6/687474703a2f2f696d672e6d792e6373646e2e6e65742f75706c6f6164732f3230313231312f33302f313335343238313038345f323232382e706e67)

##### Map的用法 #####  
* 非线程安全情景基本上都用HashMap
* 有序用TreeMap
* 线程安全优先ConcurrentHashMap
* ConcurrentHashMap和ConcurrentSkipListMap的优劣主要在于并发量，并发量不高时前者效率高，并发量很大时可以考虑后者

### Set ###
##### HashSet #####
功能： 就是一个value均为同一个PRESENT对象的HashMap
要点：   
* 非线程安全  
* 底层为一个HashMap，其所有元素value值是一个Object类型的静态常量
* 其他参考HashMap  

##### TreeSet #####
功能： 同TreeMap
要点：   
* 非线程安全  
* 底层为一个TreeMap，其所有元素value值是一个Object类型的静态常量
* 其他参考TreeMap  

##### ConcurrentSkipListSet #####
功能： 同ConcurrentSkipListMap  

##### CopyOnWriteArraySet #####
功能： 同ConcurrentSkipListMap   
要点：  
* 需要注意的是底层实现是通过CopyOnWriteArrayList，而非HashMap
* 使用场景与CopyOnWriteArrayList一样  

### Queue ###  
Queue实现了队列操作，是基本集合的一个扩充，特点是提供了队列操作offer、poll和peek，主要分为两类，一类只支持单端操作，常见的如PriorityQueue，另一类为双端队列，可实现堆栈操作，均实现Deque。需注意的是队列也提供了add和remove，但应避免使用。 

##### PriorityQueue #####  
功能：如其名，优先级队列，可根据Comparator实现优先级排列  
要点：   
* 非线程安全  
* 底层为一个Object数组
* 不提供Comparator时按照元素自然顺序  

##### ArrayDeque #####  
功能：如其名，优先级队列，可根据Comparator实现优先级排列  
要点：   
* 非线程安全  
* 底层为一个E[]数组，同时提供head和tail记录存储数据的首位位置
* 由于使用数组不是链表，速度比LinkedList更快  

##### LinkedBlockingDeque #####  
功能：阻塞队列，支持支持FIFO和FILO  
要点：   
* 线程安全  
* 底层为一个链表，通过一个ReentrantLock锁控制并发
* 可指定容量，实现阻塞，队满时插入阻塞，队空时读取阻塞，不指定则取最大整数
* 注意take在空时不阻塞直接返回失败，而poll则会阻塞
* 可指定元素超时时间

##### LinkedBlockingQueue #####  
功能：阻塞队列，和LinkedBlockingDeque基本一样，不过支持队列操作  

##### PriorityBlockingQueue #####  
功能：优先级阻塞队列，直接看名字，不解释了  

##### SynchronousQueue #####  
功能：阻塞队列，特殊之处在于其不存储元素，一个元素入队之后必须出队，否则阻塞，可以理解为1个元素的阻塞队列 

##### LinkedTransferQueue #####  
功能：LinkedBlockingQueue和SynchronousQueue的结合体，特点是如果一个元素入队过程中发现又出队请求，则直接返回，不会再插入链表中 
要点：  
* 注意其并发并非通过ReentrantLock，而是通过[LockSupport](http://my.oschina.net/readjava/blog/282882)工具类实现

##### DelayQueue #####  
功能：延迟队列，只有达到指定时间的元素才能出队  
要点：  
* 底层实现是一个PriorityQueue，通过ReentrantLock控制并发
* 可以理解为一个以入队时间-延迟时间为优先级的队列  

### 一些使用上的浅见 ###  
* 从上边的总结可以看出来java对于集合的实现非常完善，几乎所有场景都有对应的类。因此我们应该避免重复造轮子，在合适的场景下使用合适的集合不仅省力，而且更可靠
* 能简则不繁，其实多数情况下我们使用基本的几个类就够了，复杂的实现一般用于底层框架_(如JDK以及一些常用框架中就用到了不少并发的实现，但其场景都十分鲜明)_。如果平时写也想上层逻辑时使用了负责的集合类，往往我们需要认真考虑下真的有没有必要，或者这部分是不是可以抽象为一个通用的组件，这算是个很有用的代码架构技巧吧。
* 无论怎么用，理解至上。几个基本类之所以用的顺手是因为我们熟悉其特性，而对于复杂的集合类一定要理解其特性，否则很容易踩坑。集合部分算是JDK中最容易理解的代码了，花一会儿时间而保证优质代码还是很值的。

### 备注 ###
一些参考博客 _(都是很优秀的博客，若个人看源码吃力，可以参考着这些博客来看)_  
http://cmsblogs.com/?cat=5
http://my.oschina.net/lifany/blog/191294
http://blog.csdn.net/guangcigeyun/article/details/8278349  
http://www.cnblogs.com/skywang12345/p/3503480.html
http://www.infoq.com/cn/articles/java-blocking-queue/
http://my.oschina.net/readjava/blog/282882
http://hyxw5890.iteye.com/blog/1578597

_以上xmind图源文件以及该系列相关文件都将同步至[github](https://github.com/NotBadPad/learn-note/tree/master/java)，敬请关注_