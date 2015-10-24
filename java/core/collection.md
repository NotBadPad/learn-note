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
* 后进先出（LIFO）

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
* 使用CopyOnWriteArrayList，前边已经介绍了，这个只能在读多写少的场景下用，且性能很差，一般不要用
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
![](http://cmsblogs.com/wp-content/uploads/2014/04/1_thumb.png)

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
* 底层实现是一个Segment<K, V>数组，Segment内部通过一个HashEntry<K, V>数组存储元素数据，同时Segment继承了ReentrantLock，直接通过本身的锁对改段数据操作同步
* 从底层结构可以发现，其修改时不会对整个集合加锁，而是对段加锁，因此允许同时修改多个段，效率更高
* 继承NavigableMap,主要实现了SortMap，因此是有序的
* Segment的划分是通过key的hashCode计算

##### ConcurrentSkipListMap #####
功能：线程安全的有序Map，是通过一个叫做[跳表](http://blog.sina.com.cn/s/blog_72995dcc01017w1t.html)的数据结构实现 
要点：  
* 线程安全
* 底层实现是一个多层链表，每层元素都有序，每低一层都包含上一层的元素，最底层包含所有元素。这样设计的巧妙之处是从上层逐层查找时，能够快速定位元素区间。思想上有点像折半查找，不过是把每次折半的中间值分层存了起来。同时链表结构是修改操作同样高效
* 空间换时间，效率很高，但是空间占用也很厉害  
![](http://img.my.csdn.net/uploads/201211/30/1354281084_2228.png)


### Set ###



