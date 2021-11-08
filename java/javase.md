switch:

不管是char,short,byte类型都是转化为int之后走的判断。
而long ,float,double转换为int都会丢失进度，boolean无法转换为int,
所以能用在switch上的类型有，char ,byte,short,int以及相应的包装类型。
1.7支持string的switch判断 把hash值提取出来算

类变量 成员变量 局部变量


fail fast 和 fail-safe https://hollischuang.github.io/toBeTopJavaer/#/basics/java-basic/fail-fast-vs-fail-safe

CopyOnWriteArrayList
ConcurrentHashMap相当于线程安全的ArrayList，CopyOnWriteArrayList使用了一种叫写时复制的方法，当有新元素add到CopyOnWriteArrayList时，先从原有的数组中拷贝一份出来，然后在新的数组做写操作，写完之后，再将原来的数组引用指向到新数组。
CopyOnWrite并发容器用于读多写少的并发场景。比如白名单，黑名单，商品类目的访问和更新场景。

ConcurrentSkipListMap是一个内部使用跳表，并且支持排序和并发的一个Map，是线程安全的。一般很少会被用到，也是一个比较偏门的数据结构。