## Java collection

![java-collection-hierarchy](../../../download_ch/java-collection-hierarchy.png)



#### 1.ArrayList

`ArrayList`继承于 **`AbstractList`** ，实现了 **`List`**, **`RandomAccess`**, **`Cloneable`**, **`java.io.Serializable`**接口。底层使用底层使用 `Object[ ]`，其容量可以动态扩容，不保证线程安全

ArrayList采用懒加载机制，初始化大小为10，第一次add时进行初始化，每次扩容倍数为1.5

与vector的区别：Vector线程安全

添加大量元素前可以使用ensureCapacity方法

#### 2.LinkedList

LinkedList是一个实现了List接口和Deque接口的双端链表。
LinkedList底层的链表结构使它支持高效的插入和删除操作，另外它实现了Deque接口，使得LinkedList类也具有队列的特性;
LinkedList不是线程安全的，如果想使LinkedList变成线程安全的，可以调用静态类Collections类中的synchronizedList方法。

LinkedList可以用做deque来使用

```java
Deque<Integer> s2 = new LinkedList<>();
```

#### 3.HashMap

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

头插法出现死循环：线程1处于链表头，取出来上个节点A并且准备在其下面添加新的节点B。线程2进行resize，由于头插法，导致B的节点的子节点是A。最后线程1重新运行，就会配置A的子节点是B。最后导致死循环

<https://blog.csdn.net/Ho528528/article/details/103903998>

