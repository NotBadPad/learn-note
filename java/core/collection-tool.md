### 两个工具类 ###  
java.utils下又两个集合相关_(准确来说其中一个是数组的)_的工具类：Arrays和Collections，其中提供了很多针对集合的操作，其中涵盖了一下几个方面：  
* 拷贝、填充、反转等常用的基本操作  
* 排序、查找等算法相关处理  
* 安全性相关处理
* 类型转换  

下边直接用两个图来说明_(其中三言两语说不清的会标红，并在后边打上标记，在图后有对应说明)_：
#### Arrays ####
![]()  

##### sort中的多种排序算法 #####  
印象中JDK很多地方都是快排和归并，这里也不例外，不过这里用的都是优化的算法，并且根据排序元素类型策略不同：  
* [DualPivotQuicksort](http://blog.csdn.net/jy3161286/article/details/23361191?utm_source=tuicool&utm_medium=referral)   
	* 所有数值基本类型均用此排序，JDK7中新增
	* 双基准快速排，改进的多路快排算法  
* [ComparableTimSort](http://blog.csdn.net/bruce_6/article/details/38299199)  
	* 默认引用类型均用此排序，JDK7中新增
	* 基于TimSort，该算法是优化版本的归并排序，混合使用了归并和插入排序  
* [LegacyMergeSort](http://www.cnblogs.com/kkun/archive/2011/11/23/2260271.html)  
	* 老版本中的排序算法，JDK7中为兼容仍保留，若想使用可通过-Djava.util.Arrays.useLegacyMergeSort=true
	* 优化的归并排序，但是性能较TimSort差  

##### asList需注意的点 #####  
asList是我们经常使用的一个方法，可以将一组值直接转成

#### Arrays ####  
![]()  


arrays
sort
数值类型  双基准快速排——DualPivotQuicksort
对象类型 
	老的用归并排序——LegacyMergeSort  若长度小于等于INSERTIONSORT_THRESHOLD，则直接插入排序
	新的用优化的归并排序——ComparableTimSort

collections
sort 
底层调用 arrays

asList
返回list不可修改

hashCode
值的计算

deepXXX
逐层取值

unmodifiableXXXX
镜像

synchronizedXXX
包装模式

checkedXXXX
http://m.blog.csdn.net/blog/chang_harry/8759757
http://my.oschina.net/huhaonan/blog/262990