上次面试中遇到的一个问题，问到System.out.println()中的out是不是内部类，当时就给问蒙了，直观感觉out应该是System类的一个属性，跟内部类有什么关系？而且之前整理IO部分的时候记得有个PrintStream的类用于标准输出的，但是从没看过System的源码，也不敢随便再说了。后来看了下源码，发现的确是PrintStream，可能当时想问的是内部类的用法吧（真心感觉面试待靠缘分，很多面试官喜欢引导着问问题，方式很好，但很多时候可能让面试者搞不清你到底想问什么，我这次面试就深受打击，到最后面试官每个问题我都要先想半天是不是留了什么坑），不过归根结底自己水平差得多，还是要认真学习。  
言归正传，System类是jdk提供的一个工具类，有final修饰，不可继承，由名字可以看出来，其中的操作多数和系统相关。其功能主要如下：  
* 标准输入输出，如out、in、err
* 外部定义的属性和环境变量的访问，如getenv()/setenv()和getProperties()/setProperties()
* 加载文件和类库的方法，如load()和loadLibrary()、
* 一个快速拷贝数组的方法：arraycopy()
* 一些jvm操作，如gc()、runFinalization()、exit()，该部分并未在源码的java doc中提到，可能因为本身不建议主动调用吧。而且这几个方法都仅仅是Runtime.getRuntime()的调用，两者没有区别  
下边直接看图，主要的方法和功能都已经列出来。  
![](https://raw.githubusercontent.com/NotBadPad/learn-note/master/java/core/java-collection.png) 
下边我们重点来该类是如何初始化的。
首先在开头我们就可以看如下代码：
```java
    private static native void registerNatives();
    static {
        registerNatives();
    }

```
类中的静态代码块调用了一个native方法registerNatives()，可以猜到该方法应该是一个入口方法，看一下注释：通过静态初始化注册native方法，该方法会令vm通过调用initializeSystemClass方法来完成初始化工作。果然如此，那么接下来我们看下initializeSystemClass方法吧：  
```java  
private static void initializeSystemClass() {
	    // 初始化props
        props = new Properties();
        initProperties(props);  
        sun.misc.VM.saveAndRemoveProperties(props);

        //获取系统相关的换行符
        lineSeparator = props.getProperty("line.separator");
        sun.misc.Version.init();

        //分别创建in、out、err的实例对象，并通过setXX0()初始化，查看setXX0()方法可知，这是个native方法，将系统的标准流管理到类内的对象
        FileInputStream fdIn = new FileInputStream(FileDescriptor.in);
        FileOutputStream fdOut = new FileOutputStream(FileDescriptor.out);
        FileOutputStream fdErr = new FileOutputStream(FileDescriptor.err);
        setIn0(new BufferedInputStream(fdIn));
        setOut0(new PrintStream(new BufferedOutputStream(fdOut, 128), true));
        setErr0(new PrintStream(new BufferedOutputStream(fdErr, 128), true));
        //加载zip包以获取java.util.zip.ZipFile这个类，以便之后加载利库使用
        loadLibrary("zip");

        // 设置平台相关的信号处理
        Terminator.setup();

        // 初始化sun.misc相关的环境变量
        sun.misc.VM.initializeOSEnvironment();

        // 主线程不会在同一个线程组中添加相同的线程，我们必须在这里自己实现。注释半天没弄明白，看代码就是主线程自己把自己加到了自己的线程组中......
        Thread current = Thread.currentThread();
        current.getThreadGroup().add(current);

        // 注册共享秘钥？注释没看明白，该方法就是实例化一个JavaLangAccess
        setJavaLangAccess();

        // 子系统在初始化的时候可以调用sun.misc.VM.isBooted()，以保证在application类加载器启动前不做任何事。booted()其实就是改了个状态,使isBooted()变为true。
        sun.misc.VM.booted();
    }
```
至此，System基本上便讲完了，不过还有很多底层的东西没看懂(setJavaLangAccess())，留待以后解决吧。