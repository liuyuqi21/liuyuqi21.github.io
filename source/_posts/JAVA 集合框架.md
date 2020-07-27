---
title: Java 集合框架
date: 2020-01-19 15:17:43
tags: Java
categories: Java
---
容器主要包括 Collection 和 Map 两种，Collection 存储对象的集合，而 Map 存储键值对的映射表。

# Collection

![](https://gitee.com/cellophane/image/raw/master/20200412000220.png)

## Set

- TreeSet：基于红黑树实现，**保证元素的自然顺序**，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
- HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。
- LinkedHashSet：具有 HashSet 的查找效率，并且内部使用双向链表维护元素的**插入顺序**。

## List

### ArrayList 

基于动态数组，实现了 list，RandomAccess（支持快速随机访问），Cloneable（能被克隆），java.io.Serializeable（支持序列化） 接口。不支持线程安全，多线程可以使用 CopyOnWriteArrayList 或者使用 `Collections.synchronizedList();`。

- 扩容：数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的 1.5 倍。
- 删除元素：将 index + 1 后面的元素都复制到 index 位置上，时间复杂度为O(N)。
- Fail-Fast：modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出 ConcurrentModificationException。
- asList()： 参数是泛型的变长参数，不能使用基本类型数组作为参数，只能使用响应的包装类型数组，或者 `List list = Arrays.asList(1, 2, 3);`。asList() 产生的列表不可变。

### LinkedList
是一个实现了 List 和 Deque 的双向链表，只能顺序访问，但是可以快速地在链表中间插入和删除元素。

不是线程安全的，要想实现线程安全，可以调用 Collections 类中的 synchorizedList 方法。
```java
List list = Collections.synchorizedList(new LinkedList());
```

### Vector
- 和 ArrayList 类似，但它是线程安全的，使用 synchorized 进行同步。
- Vector 每次扩容请求其大小的 2 倍（可以通过构造函数设置增长的容量）。

### CopyOnWriteArrayList
读写分离：写操作再一个复制的数组上进行，读操作还是在原始数组中进行。写操作需要加锁，防止并发写入时导致数据丢失。写操作结束之后需要把原始数组指向新的复制数组。

CopyOnWriteArrayList 在写操作的同时允许读操作，大大提高了读操作的性能，适合读多写少的场景。

缺陷：
- 内存占用：写操作时需要复制一个新的数组，内存占用为原来的两倍左右
- 数据不一致：读操作不能读取实时性的数据，因为部分写擦偶哦的数据还未同步到读数组中

不适合内存敏感以及实时性要求很高的场景。

## Queue
- LinkedList：可以用它来实现双向队列。
- PriorityQueue：基于堆结构实现，可以用它来实现优先队列。
- add/offer 将元素添加到队尾（add 添加元素失败时 throw Exception，offer 返回 false 或 null）
- remove/poll 从队首获取元素并删除
- element/peek 从队首获取元素但不删除

Queue 是个接口，它提供的 add, offer 方法初衷是希望子类能够禁止添加元素为 null，这样可以避免在查询时返回 null 究竟是正确还是错误。
事实上大多数 Queue 的实现类的确响应了 Queue 接口的规定，比如 ArrayBlockingQueue，PriorityBlockingQueue 等等。
但还是有一些实现类没有这样要求，比如 LinkedList。
虽然 LinkedList 没有禁止添加 null，但是一般情况下 Queue 的实现类都不允许添加 null 元素，为啥呢？因为 poll(), peek() 方法在异常的时候会返回 null，你添加了 null　以后，当获取时不好分辨究竟是否正确返回。
add() 方法在添加失败（比如队列已满）时会报 一些运行时错误 错；而 offer() 方法即使在添加失败时也不会奔溃，只会返回 false。

# Map

![](https://gitee.com/cellophane/image/raw/master/20200412000235.png)

## HashMap

底层数据结构是数组加链表。默认容量是 16。

从 JDK 1.8 开始，一个桶存储的链表长度大于等于 8 时会将链表转换为红黑树。

### 存储结构

HashMap 中的哈希桶数组是 Node[] table，是一个 Node 数组。Node 是 HashMap 的一个内部类，实现了 Map.Entry 接口，本质是一个映射（键值对）。
```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;    //用来定位数组索引位置
    final K key;
    V value;
    Node<K,V> next;   //链表的下一个node

    Node(int hash, K key, V value, Node<K,V> next) { ... }
    public final K getKey(){ ... }
    public final V getValue() { ... }
    public final String toString() { ... }
    public final int hashCode() { ... }
    public final V setValue(V newValue) { ... }
    public final boolean equals(Object o) { ... }
}
```

### put

新来的 Entry 节点插入链表时，JDK 1.7 之前使用头插法、JDK 1.8 使用尾插法。

HashMap 最多只允许一条记录的键为 null，允许多条记录的值为 null。但是无法调用 null 的 hashCode() 方法。所以使用第 0 个桶存放 null 的键值对。

### resize
resize 方法负责初始化和扩容，衡量 HashMap 是否进行 Resize 的条件 `HashMap.Size >= Capacity * LoadFactor`
- 扩容：创建一个新的 Entry 空数组，长度是原数组的 2 倍。
- ReHash：遍历原 Entry 数组，把所有的 Entry 重新 Hash 到新数组。因为长度扩大后，Hash 规则也随之改变。

### Hash

如果通过 `key.hashCode()` 函数调用 key 键值类型自带的哈希函数，返回 int 型散列值。如果直接拿散列值作为下标访问 HashMap 主数组的话，考虑到 2 二进制 32 位带符号的 int 表值范围从 -2147483648 到 2147483648。前后加起来大概 40 亿的映射空间。但问题是一个 40 亿长度得数组，内存是放不下的。所以用之前还要先对数组得长度取模运算，得到的余数用来访问数组下标。这个取模运算在 `indexFor()` 函数里完成。

```java
static int indexFor(int h,int length){
    return h & (length-1);
}
```

Hash 公式：`index = HashCode(key) & (Length - 1)` 使用位运算方式而不采用取模，提高了性能。

```none
x   : 00010000
x-1 : 00001111

y       : 10110010
x-1     : 00001111
y&(x-1) : 00000010 
```

令一个数 y 与 x-1 做与运算，得到的结果就是截取了 y 的最低四位值，这个效果和 y 对 x 取模是一样的。

桶的数量，总容量默认值是 16，只有保证 capacity 为 **2 的 n 次方**，才能将这个操作转换为位运算。

- HashMap 允许用户传入的容量不是 2 的 n 次方，它可以自动的将传入的容量转换成 2 的 n 次方。

#### 扰动函数

但是就算散列值分布再松散，只是取最后几位的话，碰撞也会很严重，所以需要扰动函数。

```java
static final int hash(Object key) {   //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16)  为第二步 高位参与运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

这个过程参考下图

![](https://gitee.com/cellophane/image/raw/master/4acf898694b8fb53498542dc0c5f765a_720w.jpg)

右位移 16 位，正好是 32 bit 的一半。自己的高半区和低半区做异或，就是为了混合原始哈希码的高位和低位，以此来**加大低位的随机性**。

### 负载系数（load factor）

- 建议不要设置超过 0.75 的数值，因为会显著增加冲突
- 如果使用太小的负载因子，按照上面的公式，预设容量值也进行调整，否则可能会导致更加频繁的扩容。

### 高并发情况下，为什么 HashMap 可能会出现死循环
[疫苗：JAVA HASHMAP的死循环](https://coolshell.cn/articles/9606.html)
使用头插会改变链表的上的顺序，但是如果使用尾插，在扩容时会保持链表元素原本的顺序，就不会出现链表成环的问题了。所以 Java 8 不会引起死循环，原因是扩容转移后前后链表顺序不变，保持之前节点的引用关系。

## HashTable
HashTable 是线程安全的，使用 synchronized 来进行同步，不允许插入为 null 的键，但是它是遗留类不推荐使用。

与 HashMap 的比较：

- Hashtable 使用 synchronized 来进行同步。
- HashMap 可以插入键为 null 的 Entry。
- HashMap 的迭代器是 fail-fast 迭代器。
- HashMap 不能保证随着时间的推移 Map 中的元素次序是不变的。

## ConcurrentHashMap
ConcurrentHashMap 不允许键值中出现 null。

### 存储方式
JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。多线程可以同时访问不同分段锁上的桶，从而使并发度更高。默认的并发级别为 16，默认创建 16 个 Segment。
```java
static final class Segment<K,V> extends ReentrantLock implements Serializable {    
    private static final long serialVersionUID = 2249069246763182397L;    
       
    transient volatile HashEntry<K,V>[] table;// 和 HashMap 中的 HashEntry 作用一样，真正存放数据的桶     
    transient int count;          
    transient int modCount;// 记得快速失败（fail—fast）么？         
    transient int threshold;// 大小         
    final float loadFactor;// 负载因子 
    }
```

### size 操作
每个 Segment 维护了一个 count 变量来统计该 Segment 中及键值对个数。在执行 size 操作时，需要遍历所有 Segment 然后把 count 累计起来。

ConcurrentHashMap 在执行 size 操作时先尝试不加锁，如果连续两次不加锁操作得到的结果是一致的，那么可以认为这个结果是正确的。尝试次数使用 RETRIES_BEFORE_LOCK 定义，该值为 2，retries 初始值为 -1，因此尝试次数为 3。如果尝试的次数超过 3 次，就需要对每个 Segment 加锁。

### JDK 1.8 的改动
JDK 1.8 使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。

并且 JDK 1.8 的实现也在链表过长时会转换为红黑树。

## LinkedHashMap
继承自 HashMap，内部维护了一个双向链表，用来维护插入顺序或者 LRU 顺序。

accessOrder 决定顺序，false 为插入顺序，true 为 LRU，默认为 fasle。

### LRU
在 put() 等操作执行之后，会执行 afterNodeInsertion() 方法，removeEldestEntry() 为 true 时会移除最晚的节点。方法，removeEldestEntry() 默认为 fasle，如果要让他为 true，需要继承 LinkhashMap 并且覆盖这个方法的实现。
```java
private static final int MAX_ENTRIES = 3;//设置最大缓存空间

protected boolean removeEldestEntry(Map.Entry eldest){
    return size() > MAX_ENTRIES;//在节点多于 MAX_ENTRIES 时移除最近最久未使用数据
}
```

## TreeMap

基于红黑树实现

# 容器中的设计模式

## 迭代器模式
Collection 继承了 Iterable 接口，其中的 iterator() 方法能够产生一个 Iterator 对象，通过这个对象就可以迭代遍历 Collection 中的元素。

从 JDK 1.5 之后可以使用 foreach 方法来遍历实现了 Iterable 接口的聚合对象。

## 适配器模式
java.util.Arrays#asList() 可以把数组类型转换为 List 类型。

# 参考资料

- [Java Guide LinkedList](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/LinkedList.md)

- [Java Guide ArrayList](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/ArrayList.md)

- [CS-Notes Java 容器](https://cyc2018.github.io/CS-Notes/#/notes/Java%20%E5%AE%B9%E5%99%A8)

- [Java 8系列之重新认识HashMap](https://tech.meituan.com/2016/06/24/java-hashmap.html)

- [JDK 源码中 HashMap 的 hash 方法原理是什么？ - 胖君的回答 - 知乎 ](https://www.zhihu.com/question/20733617/answer/111577937)

  