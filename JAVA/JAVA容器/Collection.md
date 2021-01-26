# Collection（List、Set、Map）
## List:
### ArrayList:
- 1、线程不安全，线程安全可用Collections.synchronizedList(List l)或concurrent并发包下的CopyOnWriteArrayList类。
- 2、底层实现：数组
- 3、ArrayList是List接口的可变数组非同步实现，并允许包括null在内的所有元素，数组扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量增长大约是其容量的1.5倍
- 4、采用了Fail-Fast机制，面对并发的修改时，迭代器很快就会完全失败，而不是冒着在将来某个不确定时间发生任意不确定行为的风险
- 5、remove方法会让下标到数组末尾的元素向前移动一个单位，并把最后一位的值置空，方便GC
- 6、add、remove操作对于ArrayList其运行时间是O（N），因为在它当中在前端进行添加或移除构造新数组是O（N）操作；get方法的调用为O（1）操作。要是使用一个增强的for循环，对于任意List的运行时间都是O（N），因为迭代器将有效地从一项到下一项推进。
- 7、 Fail-Fast
modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。
在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出 ConcurrentModificationException。代码参考上节序列化中的 writeObject() 方法。
fail-fast会在以下两种情况下抛出ConcurrentModificationException：（1）单线程环境集合被创建后，在遍历它的过程中修改了结构。注意remove()方法会让expectModcount和modcount相等，所以是不会抛出这个异常。（2）多线程环境当一个线程在遍历这个集合，而另一个线程对这个集合的结构进行了修改。

---


###  Vector:
### CopyOnWriteArrayList
- List<String> list = new CopyOnWriteArrayList<>();
- 读写分离：写操作在一个复制的数组上进行，读操作还是在原始数组中进行，读写分离，互不影响。
写操作需要加锁，防止并发写入时导致写入数据丢失。
写操作结束之后需要把原始数组指向新的复制数组。
- 适用场景：CopyOnWriteArrayList 在写操作的同时允许读操作，大大提高了读操作的性能，因此很适合读多写少的应用场景。但是CopyOnWriteArrayList 有其缺陷：内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。所以CopyOnWriteArrayList不适合内存敏感以及对实时性要求很高的场景。

### LinkList:
```
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```
- 1、双向链表
- 

---

## Set:
### HashSet:
### TreeSet:
### LinkHashSet:


---


## Map:
### HashMap:
```
// 默认容量
static final int DEFAULT_INITIAL_CAPACITY = 16;

// 最大容量
static final int MAXIMUM_CAPACITY = 1 << 30;

// 默认负载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;

transient Entry[] table;

transient int size;

int threshold;

final float loadFactor;

// 数据结构变化计数
transient int modCount;
```
- 扩容时，capacity扩为原来的两倍
- 


### ConcurrentHashMap:

```
static final class HashEntry<K,V> {
    final int hash;
    final K key;
    volatile V value;
    volatile HashEntry<K,V> next;
}
```
- ConcurrentHashMap 和 HashMap 实现上类似，最主要的差别是 ConcurrentHashMap 采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶，从而使其并发度更高（并发度就是 Segment 的个数）。

- Segment 继承自 ReentrantLock。
- 默认的并发级别为 16，也就是说默认创建 16 个 Segment
```
static final int DEFAULT_CONCURRENCY_LEVEL = 16;
```
- 在执行 size 操作时，需要遍历所有 Segment 然后把 count 累计起来。
ConcurrentHashMap 在执行 size 操作时先尝试不加锁，如果连续两次不加锁操作得到的结果一致，那么可以认为这个结果是正确的。
尝试次数使用 RETRIES_BEFORE_LOCK 定义，该值为 2，retries 初始值为 -1，因此尝试次数为 3。
如果尝试的次数超过 3 次，就需要对每个 Segment 加锁。
- JDK 1.8 的改动：
JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。JDK 1.8 使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。并且 JDK 1.8 的实现也在链表过长时会转换为红黑树。

### HashTable:
### TreeMap:
### LinkHashMap:






