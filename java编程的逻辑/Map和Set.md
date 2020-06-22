# Map和Set

## 1、HashMap默认构造？

DEFAULT_INITIAL_CAPACITY为16, DEFAULT_LOAD_FACTOR为0.75，默认构造方法调用的构造方法主要代码为：

## 2、HashMap保存键值对？（java8）

~~~java
 public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
//计算hash值
 static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);//让第十六位和高十六位 都参与计算  这样计算的hash值不容易冲突
    }
 final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;//扩容后的桶长度
        if ((p = tab[i = (n - 1) & hash]) == null)//计算余数
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }



final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;//默认初始化 大小16
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);//默认初始化 负载因子为12
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
~~~

## 3、HashMap保存键值对？（java-7）

~~~java
//添加键值对
public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
             //设置桶的容量
            inflateTable(threshold);
        }
        if (key == null)
            return putForNullKey(value);
    
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            //遍历当前桶的所有节点信息（包括hash是否相同并且key也相同） 覆盖前一个值
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
 //设置桶的容量
 private void inflateTable(int toSize) {
        // Find a power of 2 >= toSize
        int capacity = roundUpToPowerOf2(toSize);

        threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        table = new Entry[capacity];
        initHashSeedAsNeeded(capacity);
    }
//获取容量大小
 private static int roundUpToPowerOf2(int number) {
        // assert number >= 0 : "number must be non-negative";
        int rounded = number >= MAXIMUM_CAPACITY
                ? MAXIMUM_CAPACITY
                : (rounded = Integer.highestOneBit(number)) != 0
                    ? (Integer.bitCount(number) > 1) ? rounded << 1 : rounded
                    : 1;

        return rounded;
    }
//根据key获取hash值 减少hash冲突的概率和值分布均匀
 final int hash(Object k) {
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        h ^= k.hashCode();

        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }

 /**
     * 根据hash值获取 数组的索引
     */
    static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);
    }
//先判断是否需要扩容   然后添加节点
 void addEntry(int hash, K key, V value, int bucketIndex) {
     //同时满足键值对数量大于等于阈值并且当前索引对应的桶至少存在一个节点
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
//直接添加键值对 
  void createEntry(int hash, K key, V value, int bucketIndex) {
      //设置当前桶的值 直接插入链表头部 记住当前头节点 作为当前节点的下一个节点
        Entry<K,V> e = table[bucketIndex];
        table[bucketIndex] = new Entry<>(hash, key, value, e);
        size++;
    }
//扩容
void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
//复制原来桶中的元素放到新的桶中
 /**
     * Transfers all entries from current table to newTable.
     */
    void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }

 /**
     * 支持给key为null 赋值 如果多个key为null 后者覆盖前者
     */
    private V putForNullKey(V value) {
        for (Entry<K,V> e = table[0]; e != null; e = e.next) {
            if (e.key == null) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;
        addEntry(0, null, value, 0);
        return null;
    }
~~~

## 4、HashSet使用场景？

1）排重，如果对排重后的元素没有顺序要求，则HashSet可以方便地用于排重；

2）保存特殊值，Set可以用于保存各种特殊值，程序处理用户请求或数据记录时，根据是否为特殊值判断是否进行特殊处理，比如保存IP地址的黑名单或白名单；

3）集合运算，使用Set可以方便地进行数学集合中的运算，如交集、并集等运算，这些运算有一些很现实的意义。比如，用户标签计算，每个用户都有一些标签，两个用户的标签交集就表示他们的共同特征，交集大小除以并集大小可以表示他们的相似程度。

## 5、HashSet实现原理？

HashSet内部是用HashMap实现的

就是调用map的put方法，元素e用于键，值就是固定值PRESENT, put返回null表示原来没有对应的键，添加成功了

本节介绍了HashSet的用法和实现原理，它实现了Set接口，内部实现利用了HashMap，有如下特点：

1）没有重复元素；

2）可以高效地添加、删除元素、判断元素是否存在，效率都为O(1)；

3）没有顺序。HashSet可以方便高效地实现去重、集合运算等功能。如果要保持添加的顺序，可以使用HashSet的一个子类LinkedHashSet。Set还有一个重要的实现类TreeSet，它可以排序。这两个类，我们在后续小节介绍。

## 6、排序二叉树 （非递归遍历）

不用递归的方式，也可以实现按序遍历，第一个节点为最左边的节点，从第一个节点开始，依次找后继节点。给定一个节点，找其后继节点的算法为：

1）如果该节点有右孩子，则后继节点为右子树中最小的节点。

2）如果该节点没有右孩子，则后继节点为父节点或某个祖先节点，从当前节点往上找，如果它是父节点的右孩子，则继续找父节点，直到它不是右孩子或父节点为空，第一个非右孩子节点的父节点就是后继节点，如果找不到这样的祖先节点，则后继为空，遍历结束。

排序二叉树的基本概念和算法。

排序二叉树保持了元素的顺序，而且是一种综合效率很高的数据结构，基本的保存、删除、查找的效率都为O(h), h为树的高度。在树平衡的情况下，h为log2(N), N为节点数。比如，如果N为1024，则log2(N)为10。基本的排序二叉树不能保证树的平衡，可能退化为一个链表。有很多保持树平衡的算法，AVL树能保证树的高度平衡，但红黑树是实际中使用更为广泛的，虽然红黑树只能保证大致平衡，但降低了维持树平衡需要的开销，整体统计效果更好。与哈希表一样，树也是计算机程序中一种重要的数据结构和思维方式。为了能够快速操作数据，哈希和树是两种基本的思维方式，不需要顺序，优先考虑哈希，需要顺序，考虑树。除了容器类TreeMap/TreeSet，数据库中的索引结构也是基于树的（不过基于B树，而不是二叉树），而索引是能够在大量数据中快速访问数据的关键。理解了排序二叉树的基本概念和算法，理解TreeMap和TreeSet就比较容易了，让我们在接下来的小节中探讨这两个类。

## 7、TreeMap保存键值对？

~~~java
 public V put(K key, V value) {
     //保存根节点
        Entry<K,V> t = root;
        if (t == null) {
            compare(key, key); // 其实，这里的目的不是为了比较，而是为了检查key的类型和null，如果类型不匹配或为null，那么compare方法会抛出异常。
            root = new Entry<>(key, value, null);
            size = 1;
            modCount++;
            return null;
        }
        int cmp;
        Entry<K,V> parent;
        // 判断是否是实现了Comparator的类
        Comparator<? super K> cpr = comparator;
        if (cpr != null) {
            do {
                //开始寻找父节点
                parent = t;
                cmp = cpr.compare(key, t.key);
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else
                    return t.setValue(value);
            } while (t != null);
        }
        else {
            //键不能为空  
            if (key == null)
                throw new NullPointerException();
            Comparable<? super K> k = (Comparable<? super K>) key;
            do {
                parent = t;
                cmp = k.compareTo(t.key);
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else
                    return t.setValue(value);
            } while (t != null);//从根节点开始遍历所有节点 进行键比对
        }
        Entry<K,V> e = new Entry<>(key, value, parent);
        if (cmp < 0)
            parent.left = e;
        else
            parent.right = e;
        fixAfterInsertion(e);
        size++;
        modCount++;
        return null;
    }

 /**
     * 使用正确的比较方法比较TreeMap中的两个键。 
     */
    final int compare(Object k1, Object k2) {
        return comparator==null ? ((Comparable<? super K>)k1).compareTo((K)k2)
            : comparator.compare((K)k1, (K)k2);
    }
/** 红黑树着色及其左右旋转 */
    private void fixAfterInsertion(Entry<K,V> x) {
        x.color = RED;

        while (x != null && x != root && x.parent.color == RED) {
            if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
                Entry<K,V> y = rightOf(parentOf(parentOf(x)));
                if (colorOf(y) == RED) {
                    setColor(parentOf(x), BLACK);
                    setColor(y, BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    x = parentOf(parentOf(x));
                } else {
                    if (x == rightOf(parentOf(x))) {
                        x = parentOf(x);
                        rotateLeft(x);
                    }
                    setColor(parentOf(x), BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    rotateRight(parentOf(parentOf(x)));
                }
            } else {
                Entry<K,V> y = leftOf(parentOf(parentOf(x)));
                if (colorOf(y) == RED) {
                    setColor(parentOf(x), BLACK);
                    setColor(y, BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    x = parentOf(parentOf(x));
                } else {
                    if (x == leftOf(parentOf(x))) {
                        x = parentOf(x);
                        rotateRight(x);
                    }
                    setColor(parentOf(x), BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    rotateLeft(parentOf(parentOf(x)));
                }
            }
        }
        root.color = BLACK;
    }

~~~

代码大部分都容易理解，不过，里面有一行重要调用fixAfterInsertion(e);，它就是在调整树的结构，使之符合红黑树的约束，保持大致平衡，其代码我们就不介绍了

稍微总结一下，其基本思路就是：循环比较找到父节点，并插入作为其左孩子或右孩子，然后调整保持树的大致平衡。

## 8、TreeMap返回最第一个节点

~~~java
/**
     * Returns the first Entry in the TreeMap (according to the TreeMap's
     * key-sort function).  Returns null if the TreeMap is empty.
     */
    final Entry<K,V> getFirstEntry() {
        Entry<K,V> p = root;
        if (p != null)
            while (p.left != null)
                p = p.left;
        return p;
    }
~~~

## 9、 TreeMap返回后继节点

~~~java
 /**
     * Returns the successor of the specified Entry, or null if no such.
     */
    static <K,V> TreeMap.Entry<K,V> successor(Entry<K,V> t) {
        if (t == null)
            return null;
        else if (t.right != null) {
            Entry<K,V> p = t.right;
            while (p.left != null)
                p = p.left;
            return p;
        } else {
            Entry<K,V> p = t.parent;
            Entry<K,V> ch = t;
            while (p != null && ch == p.right) {
                ch = p;
                p = p.parent;
            }
            return p;
        }
    }
//1）如果该节点有右孩子，则后继节点为右子树中最小的节点。

2）如果该节点没有右孩子，则后继节点为父节点或某个祖先节点，从当前节点往上找，如果它是父节点的右孩子，则继续找父节点，直到它不是右孩子或父节点为空，第一个非右孩子节点的父节点就是后继节点，如果找不到这样的祖先节点，则后继为空，遍历结束。
~~~

## 10、TreeMap删除节点

1）叶子节点：这个容易处理，直接修改父节点对应引用置null即可。

2）只有一个孩子：就是在父亲节点和孩子节点直接建立链接。

3）有两个孩子：先找到后继节点，找到后，替换当前节点的内容为后继节点，然后再删除后继节点，因为这个后继节点一定没有左孩子，所以就将两个孩子的情况转换为了前面两种情况。

复杂问题   转化   简单问题

## 11、LRU(最近 最少使用)

LinkedHashMap  一般而言，缓存容量有限，不能无限存储所有数据，如果缓存满了，当需要存储新数据时，就需要一定的策略将一些老的数据清理出去，这个策略一般称为替换算法。LRU是一种流行的替换算法，它的全称是LeastRecently Used，即最近最少使用。它的思路是，最近刚被使用的很快再次被用的可能性最高，而最久没被访问的很快再次被用的可能性最低，所以被优先清理。

## 12、怎么采用LinkHashMap实现LRU算法 ？

在添加元素到LinkedHashMap后，LinkedHashMap会调用这个方法，传递的参数是最久没被访问的键值对，如果这个方法返回true，则这个最久的键值对就会被删除。Linked-HashMap的实现总是返回false，所有容量没有限制，但子类可以重写该方法，在满足一定条件的情况，返回true。

## 13、LinkHashMap实现原理？

LinkedHashMap的基本实现原理，它是HashMap的子类，它的节点类LinkedHashMap.Entry是HashMap.Entry的子类，LinkedHashMap内部维护了一个单独的双向链表，每个节点即位于哈希表中，也位于双向链表中，在链表中的顺序默认是插入顺序，也可以配置为访问顺序，LinkedHashMap及其节点类LinkedHashMap.Entry重写了若干方法以维护这种关系。

## 14、EnumMap小结

本节介绍了EnumMap的用法和实现原理，用法上，如果需要一个Map且键是枚举类型，则应该用它，简洁、方便、安全；实现原理上，内部有两个数组，长度相同，一个表示所有可能的键，一个表示对应的值，值为null表示没有该键值对，键都有一个对应的索引，根据索引可直接访问和操作其键和值，效率很高。

## 15、EnumSet了解一下

EnumSet是使用位向量实现的，什么是位向量呢？就是用一个位表示一个元素的状态，用一组位表示一个集合的状态，每个位对应一个元素，而状态只可能有两种。

Java中有一个更为通用的可动态扩展长度的位向量容器类BitSet，可以方便地对指定位置的位进行操作，与其他位向量进行位运算，具体可参看API文档，我们就不介绍了。

## 16、BitSet了解一下

## 17、完全二叉树特性？

给定任意一个节点，可以根据其编号直接快速计算出其父节点和孩子节点编号。如果编号为i，则父节点编号即为i/2，左孩子编号即为2× i，右孩子编号即为2× i+1。比如，对于5号节点，父节点为5/2即2，左孩子为2× 5即10，右孩子为2× 5+1即11。

## 18、求前K个最大元素

一个简单的思路是排序，排序后取最大的K个就可以了，排序可以使用Arrays.sort()方法，效率为O(N×log2(N))。不过，如果K很小，比如是1，就是取最大值，对所有元素完全排序是毫无必要的。另一个简单的思路是选择，循环选择K次，每次从剩下的元素中选择最大值，这个效率为O(N×K)，如果K的值大于log2(N)，这个就不如完全排序了。

不过，这两个思路都假定所有元素都是已知的，而不是动态添加的。如果元素个数不确定，且源源不断到来呢？

解决方法是使用最小堆维护这K个元素，最小堆中，根即第一个元素永远都是最小的，新来的元素与根比就可以了，如果小于根，则堆不需要变化，否则用新元素替换根，然后向下调整堆即可，调整的效率为O(log2(K))，这样，总体的效率就是O(N×log2(K))，这个效率非常高，而且存储成本也很低。