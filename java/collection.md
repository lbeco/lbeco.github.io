# Java collection



<img src="C:\Users\Linbc\Desktop\CS\JavaGuide-master\docs\java\collection\images\Java-Collections.jpeg" alt="Java-Collections" style="zoom: 67%;" />

### List

#### ArrayList

`ArrayList`继承于 **`AbstractList`** ，实现了 **`List`**, **`RandomAccess`**, **`Cloneable`**, **`java.io.Serializable`**接口。底层使用底层使用 `Object[ ]`，其容量可以动态扩容，不保证线程安全

ArrayList采用懒加载机制，初始化大小为10，第一次add时进行初始化，每次扩容倍数为1.5

与vector的区别：Vector线程安全

添加大量元素前可以使用ensureCapacity方法

#### LinkedList

LinkedList是一个实现了List接口和Deque接口的双端链表。
LinkedList底层的链表结构使它支持高效的插入和删除操作，另外它实现了Deque接口，使得LinkedList类也具有队列的特性;
LinkedList不是线程安全的，如果想使LinkedList变成线程安全的，可以调用静态类Collections类中的synchronizedList方法。

**LinkedList可以用做deque来使用**

```java
Deque<Integer> s2 = new LinkedList<>();
```

#### Vector

vector类似arrayList，底层也是由Object[]实现的。

#### CopyOnWriteArrayList

CopyOnWriteArrayList是一个线程安全，读操作时无锁的ArrayList。在put或set改变数组时，都会对底层数组进行复制。

### Map

#### HashMap

##### 基本结构：

1.8之前HashMap使用链表散列，经过扰动以后得到hash值，然后判断元素存放的位置

##### 散列方法：

 1.8进行简化：

```java
static final int hash(Object key) {
      int h;
      // key.hashCode()：返回散列值也就是hashcode
      // ^ ：按位异或
      // >>>:无符号右移，忽略符号位，空位都以0补齐
      return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
  }
```

对比1.7：

```java
static int hash(int h) {
    // This function ensures that hashCodes that differ only by
    // constant multiples at each bit position have a bounded
    // number of collisions (approximately 8 at default load factor).

    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}
```

扰动是为了防止部分hash位置被太多元素放入

##### 红黑树：

HashMap在1.8引入红黑树，当链表长度大于阈值（默认为 8）时，会首先调用 treeifyBin()方法。这个方法会根据 HashMap 数组来决定是否转换为红黑树。只有当数组长度大于或者等于 64 的情况下，才会执行转换红黑树操作，以减少搜索时间。

1.7&1.8 头插法和尾插法<https://blog.csdn.net/weixin_42373997/article/details/112085344>

头插法出现死循环原因：

线程1处于链表头，取出来上个节点A并且准备在其下面添加新的节点B。

线程2进行**resize**，由于头插法，导致B的节点的子节点是A。最后线程1重新运行，就会配置A的子节点是B。最后导致死循环

<https://blog.csdn.net/Ho528528/article/details/103903998>

#### HashTable

hashtable是线程安全的类，每一个方法都经过synchronized修饰，性能较慢。

HashMap可以使用null作为key，而Hashtable则不允许null作为key

HashTable初始值为11，Hashtable扩容时是容量翻倍+1即:capacity2+1

底层实现都是数组+链表结构实现

#### ConcurrentHashMap

  ConcurrentHashMap经典八股文，用于并发状态下的map

参见 .\reference\ConcurrentHashMap源码+底层数据结构分析.md

##### 1.7版本

ConcurrentHashMap在1.7中采用segment做为并发的实现，segment本身就类似一个新的hash表，包含着一个hashEntry数组，但是其本身不可扩容。默认的Segment数量是16。Segment 实现了 ReentrantLock，对其进行修改时必须先获得对应的锁。

##### 1.8版本

ConcurrentHashMap 取消了 Segment 分段锁，采用 CAS 和 synchronized 来保证并发安全。数据结构跟 HashMap1.8 的结构类似，Node数组+链表/红黑二叉树。Java 8 在链表长度超过一定阈值（8）时将链表（寻址时间复杂度为 O(N)）转换为红黑树（寻址时间复杂度为 O(log(N))）

synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，效率又提升 N 倍。



**put方法**

1. 根据 key 计算出 hashcode 。
2. 判断是否需要进行初始化。
3. 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 CAS 尝试写入，失败则自旋保证成功。
4. 如果当前位置的 `hashcode == MOVED == -1`,则需要进行扩容。
5. 如果都不满足，则利用 synchronized 锁写入数据。tab中为链表或红黑树时就会使用synchronized 
6. 如果数量大于 `TREEIFY_THRESHOLD` 则要转换为红黑树。



**如何保持扩容时的线程安全**

1. 拷贝槽点时，会把原数组的槽点锁住 // synchronized (f)；
2. 拷贝成功之后，会把原数组的槽点设置成转移节点（FowardingNode）。
3. 从尾到头进行拷贝，拷贝成功就把原数组的槽点设置成转移节点。
4. 等扩容拷贝都完成之后，直接把新数组的值赋值给数组容器，之前等待 put 的数据才能继续
   put。

**协助扩容**

在执行put操作的线程中，第一个发现需要扩容的线程负责分配新数组、开始转移部分Node，每次处理一个bin；此后，其他发现有resize正在进行中的线程参与到转移Node工作；其他也在执行put操作但不参与转移工作的线程继续执行原来的put操作（先在原数组中找到bin，如果遇到FowardingNode，则在新数组中插入）；执行get操作的线程不参与转移工作，遇到FordwardingNode则到新数组查询。

**计数机制**

为了能够记录ConcurrentHashMap中的key-value个数，ConcurrentHashMap在它的内部实现了一个类似于LongAdder的高性能计数器。利用的是sumCount()方法来进行计算。sumCount是baseCount加上counterCells。

当进行添加的时候，使用fullAddCount()函数。其会首先试图cas相应的counterCells，加不上去再试图cas加baseCount

#### LinkedHashMap

继承了hashmap，在hashmap的散列结构中添加了一条双向链表，可以保持map的插入顺序。

可以拿来实现lru,但是由于LinkedHashMap实现了扩容，所以不能直接实现lru

若设置 accessOrder = true, 则LinkedHashMap会被设置为访问序

顺序应当最前的会被放在队尾



#### TreeMap

treeMap以红黑树为底层，数据默认已经进行了排序。

主要方法：

```java
ceilingEntry()  // 返回具有(大于或等于指定键)最小键值的键值对,可以包括自己。如果没有这样的键,则返回null
higherEntry()   // 返回具有(大于指定键)最小键值的键值对,不可以包括自己。如果没有这样的键,则返回null
getFloorEntry()	//较小的键值对，可以包括自己
lowerEntry()    //较小的键值对，不包括自己
```

#### **ConcurrentSkipListMap**

跳表，key有序，key和value都不可以为null。和TreeeMap类似

### Queue

双端口Queue的接口是Deque

#### ArrayDeque

ArrayDeque是 Deque的实现类

#### PriorityQueue

优先级队列

示例：

```java
PriorityQueue<Integer> p = new PriorityQueue<>((a,b)->b-a);// 大顶堆
PriorityQueue<Integer> p = new PriorityQueue<>((a,b)->a-b);// 小顶堆
```

可见，比较时返回结果若为真，则第二个参数会排在前面

或者：

```java
Comparator<Integer> comp = new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o1-o2;
    }
};
PriorityQueue<Integer> p2 = new PriorityQueue<>(comp);
```

#### **LinkedBlockingDeque**

LinkedBlockingDeque是一个由链表结构组成的双向阻塞队列，即可以从队列的两端插入和移除元素。在不能够插入元素时，它将阻塞住试图插入元素的线程；在不能够抽取元素时，它将阻塞住试图抽取的线程。支持线程安全
