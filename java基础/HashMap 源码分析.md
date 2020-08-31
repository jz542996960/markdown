## HashMap 源码分析

1. 调用无参构造函数，构造一个HashMap

```java
 // DEFAULT_INITIAL_CAPACITY 默认的数组长度为16，DEFAULT_LOAD_FACTOR 默认的加载因子为0.75
 public HashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
 }
 
 
//构造hashMap
 public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        // 加载因子
        this.loadFactor = loadFactor;
        //默认长度
        threshold = initialCapacity;
        init();
 }

// init 为空 linkhashmap  重写了改方法
void init() {
 }

```

2.  调用put 方法

```java
    public V put(K key, V value) {
        // 判断table是否为空，为空就初始化table
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        //放入key为null的元素，放在table[0]里
        if (key == null)
            return putForNullKey(value);
        //根据key 计算hash值
        int hash = hash(key);
        // 根据hash值 和数组长度计算需要放入的数组的下标
        int i = indexFor(hash, table.length);
        // 遍历链表,如果已经key相同的元素（hash值相同 并且 (== 或者 equal 相同）,就覆盖原值，返回老的元素值
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;
        // 没有key相同的元素，就实例化entry，加入链表里。
        addEntry(hash, key, value, i);
        //返回空值
        return null;
    }
```



2.1 inflateTable 初始化table

```java
//入参 初始化的数组长度
private void inflateTable(int toSize) {
    //根据需要初始化数组长度，所以要找到“大于”toSize的“最小的2的n次方”
    int capacity = roundUpToPowerOf2(toSize);
    // 计算扩容阈值，扩容阈值 =  数组长度 *  加载因子
    threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
    // 初始化数组长度
    table = new Entry[capacity];
    initHashSeedAsNeeded(capacity);
}
```



2.2 roundUpToPowerOf2 (toSize)

```java
   private static int roundUpToPowerOf2(int number) {
        //计算查找接近入参的2的次方数
        return number >= MAXIMUM_CAPACITY
                ? MAXIMUM_CAPACITY
                : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
    }
```

Integer.highestOneBit  作用计算查找小于等于入参的2的次方数

```java
public static int highestOneBit(int i) {
    // HD, Figure 3-1
    i |= (i >>  1);
    i |= (i >>  2);
    i |= (i >>  4);
    i |= (i >>  8);
    i |= (i >> 16);
    return i - (i >>> 1);
}
```

````java
    public static void main(String[] args) {
        System.out.println(Integer.highestOneBit(6));  // 4
        System.out.println(Integer.highestOneBit(8));  // 8
        System.out.println(Integer.highestOneBit(10)); // 8
        System.out.println(Integer.highestOneBit(31)); //16
    }
````



比如传入 10,  二级制为 0000 1010, 最终计算出来是8

```
//i |= (i >>  1);
0000 1010
0000 0101
0000 1111   

// i |= (i >>  2);
0000 1111
0000 0011
0000 1111

//不会变
0000 1111
0000 0111
0000 1000  == 8
```

 

2.3 putForNullKey  (HashMap 的key 可以为null)

```java
private V putForNullKey(V value) {
    // key 为null 的entity，放在数组下标为0 的位置
    // 遍历下标为0 处的链表，如果找到key 为null的，就覆盖原值，返回老的值
    for (Entry<K,V> e = table[0]; e != null; e = e.next) {
        if (e.key == null) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }
    modCount++;
    // 如果没找到就头插法加入链表里，并返回空值
    addEntry(0, null, value, 0);
    return null;
}
```



2.4   计算key的hash值后进行左移和^(相同为1，不同为0)，是为了把key的hashcode的高位移动到低位，是高位也参数到index的计算，使得hash值 更散列，最终计算出的index 也更散列

```java
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
```

2.5  计算index

```java
    /**
     * Returns index for hash code h.
     */
    static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);
    }

```

   计算 下标是通过   hash值 和 数组长度减一 进行与运算 计算出的，目的是为了把数据分配的更均匀