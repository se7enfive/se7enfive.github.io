---
layout: post
title: Java Map集合详解
subtitle: HashMap、TreeMap、LinkedHashMap、Hashtable、ConcurrentHashMap的特点与使用场景
date: 2018-09-07
categories: 技术
tags: java map 集合 数据结构
cover: 
---

Map是Java集合框架中用于存储键值对的接口，理解HashMap、TreeMap、LinkedHashMap、Hashtable、ConcurrentHashMap的特点和使用场景对于Java开发至关重要。

## Map接口概述

### Map接口特点
- **键值对存储**：存储键值对映射
- **键唯一性**：键不能重复
- **值可重复**：值可以重复
- **无序性**：元素没有顺序（除了LinkedHashMap）

### Map接口定义
```java
public interface Map<K,V> {
    // 基本操作
    int size();
    boolean isEmpty();
    boolean containsKey(Object key);
    boolean containsValue(Object value);
    V get(Object key);
    V put(K key, V value);
    V remove(Object key);
    void putAll(Map<? extends K, ? extends V> m);
    void clear();
    
    // 视图操作
    Set<K> keySet();
    Collection<V> values();
    Set<Map.Entry<K,V>> entrySet();
    
    // 内部接口
    interface Entry<K,V> {
        K getKey();
        V getValue();
        V setValue(V value);
        boolean equals(Object o);
        int hashCode();
    }
}
```

## HashMap（数组+链表+红黑树实现）

### HashMap特点
- **基于哈希表**：底层使用数组+链表+红黑树实现
- **无序性**：元素没有顺序
- **高效查找**：O(1)时间复杂度的查找
- **非线程安全**：不是线程安全的

### HashMap实现原理
```java
public class HashMap<K,V> extends AbstractMap<K,V>
        implements Map<K,V>, Cloneable, java.io.Serializable {
    
    static final int DEFAULT_INITIAL_CAPACITY = 16;
    static final int MAXIMUM_CAPACITY = 1 << 30;
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;
    
    transient Node<K,V>[] table;
    transient int size;
    int threshold;
    final float loadFactor;
    
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        
        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }
}
```

### HashMap核心方法
```java
public class HashMapExample {
    public void demonstrateHashMap() {
        // 创建HashMap
        HashMap<String, Integer> map = new HashMap<>();
        
        // 添加键值对
        map.put("Java", 1);
        map.put("Python", 2);
        map.put("C++", 3);
        
        // 获取值
        Integer value = map.get("Java");
        
        // 检查是否包含键
        boolean containsKey = map.containsKey("Java");
        
        // 检查是否包含值
        boolean containsValue = map.containsValue(1);
        
        // 删除键值对
        Integer removed = map.remove("Java");
        
        // 获取大小
        int size = map.size();
        
        // 检查是否为空
        boolean isEmpty = map.isEmpty();
        
        // 清空映射
        map.clear();
    }
}
```

### HashMap性能特点
- **添加操作**：O(1)时间复杂度
- **删除操作**：O(1)时间复杂度
- **查找操作**：O(1)时间复杂度
- **遍历操作**：O(n)时间复杂度

## TreeMap（红黑树实现）

### TreeMap特点
- **基于红黑树**：底层使用红黑树实现
- **有序性**：元素按照自然顺序或比较器顺序排列
- **高效查找**：O(log n)时间复杂度的查找
- **非线程安全**：不是线程安全的

### TreeMap实现原理
```java
public class TreeMap<K,V> extends AbstractMap<K,V>
        implements NavigableMap<K,V>, Cloneable, java.io.Serializable {
    
    private final Comparator<? super K> comparator;
    private transient Entry<K,V> root;
    private transient int size = 0;
    private transient int modCount = 0;
    
    static final class Entry<K,V> implements Map.Entry<K,V> {
        K key;
        V value;
        Entry<K,V> left;
        Entry<K,V> right;
        Entry<K,V> parent;
        boolean color = BLACK;
        
        Entry(K key, V value, Entry<K,V> parent) {
            this.key = key;
            this.value = value;
            this.parent = parent;
        }
    }
}
```

### TreeMap核心方法
```java
public class TreeMapExample {
    public void demonstrateTreeMap() {
        // 创建TreeMap
        TreeMap<String, Integer> map = new TreeMap<>();
        
        // 添加键值对
        map.put("Java", 1);
        map.put("Python", 2);
        map.put("C++", 3);
        map.put("JavaScript", 4);
        
        // 获取第一个键值对
        Map.Entry<String, Integer> first = map.firstEntry();
        
        // 获取最后一个键值对
        Map.Entry<String, Integer> last = map.lastEntry();
        
        // 获取小于指定键的最大键值对
        Map.Entry<String, Integer> lower = map.lowerEntry("Python");
        
        // 获取大于指定键的最小键值对
        Map.Entry<String, Integer> higher = map.higherEntry("Python");
        
        // 获取子映射
        SortedMap<String, Integer> subMap = map.subMap("C++", "Python");
        
        // 获取头部子映射
        SortedMap<String, Integer> headMap = map.headMap("Python");
        
        // 获取尾部子映射
        SortedMap<String, Integer> tailMap = map.tailMap("Python");
    }
}
```

### TreeMap性能特点
- **添加操作**：O(log n)时间复杂度
- **删除操作**：O(log n)时间复杂度
- **查找操作**：O(log n)时间复杂度
- **遍历操作**：O(n)时间复杂度

## LinkedHashMap（哈希表+链表实现）

### LinkedHashMap特点
- **基于哈希表和链表**：底层使用HashMap+双向链表实现
- **有序性**：保持插入顺序或访问顺序
- **高效查找**：O(1)时间复杂度的查找
- **非线程安全**：不是线程安全的

### LinkedHashMap实现原理
```java
public class LinkedHashMap<K,V> extends HashMap<K,V>
        implements Map<K,V> {
    
    static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }
    
    transient Entry<K,V> head;
    transient Entry<K,V> tail;
    final boolean accessOrder;
    
    public LinkedHashMap() {
        super();
        accessOrder = false;
    }
    
    public LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder) {
        super(initialCapacity, loadFactor);
        this.accessOrder = accessOrder;
    }
}
```

### LinkedHashMap核心方法
```java
public class LinkedHashMapExample {
    public void demonstrateLinkedHashMap() {
        // 创建LinkedHashMap（保持插入顺序）
        LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
        
        // 添加键值对
        map.put("Java", 1);
        map.put("Python", 2);
        map.put("C++", 3);
        
        // 遍历键值对（保持插入顺序）
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }
        
        // 创建访问顺序的LinkedHashMap
        LinkedHashMap<String, Integer> accessOrderMap = new LinkedHashMap<>(16, 0.75f, true);
        accessOrderMap.put("Java", 1);
        accessOrderMap.put("Python", 2);
        accessOrderMap.put("C++", 3);
        
        // 访问元素会改变顺序
        accessOrderMap.get("Java");
        // 现在Java会移动到末尾
    }
}
```

### LinkedHashMap性能特点
- **添加操作**：O(1)时间复杂度
- **删除操作**：O(1)时间复杂度
- **查找操作**：O(1)时间复杂度
- **遍历操作**：O(n)时间复杂度

## Hashtable（线程安全的哈希表实现）

### Hashtable特点
- **基于哈希表**：底层使用哈希表实现
- **线程安全**：所有方法都是同步的
- **无序性**：元素没有顺序
- **性能较低**：由于同步机制，性能较低

### Hashtable实现原理
```java
public class Hashtable<K,V> extends Dictionary<K,V>
        implements Map<K,V>, Cloneable, java.io.Serializable {
    
    private transient Entry<?,?>[] table;
    private transient int count;
    private int threshold;
    private float loadFactor;
    private transient int modCount = 0;
    
    private static class Entry<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Entry<K,V> next;
        
        protected Entry(int hash, K key, V value, Entry<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }
}
```

### Hashtable核心方法
```java
public class HashtableExample {
    public void demonstrateHashtable() {
        // 创建Hashtable
        Hashtable<String, Integer> table = new Hashtable<>();
        
        // 添加键值对
        table.put("Java", 1);
        table.put("Python", 2);
        table.put("C++", 3);
        
        // 获取值
        Integer value = table.get("Java");
        
        // 检查是否包含键
        boolean containsKey = table.containsKey("Java");
        
        // 检查是否包含值
        boolean containsValue = table.containsValue(1);
        
        // 删除键值对
        Integer removed = table.remove("Java");
        
        // 获取大小
        int size = table.size();
        
        // 检查是否为空
        boolean isEmpty = table.isEmpty();
        
        // 清空映射
        table.clear();
    }
}
```

### Hashtable性能特点
- **添加操作**：O(1)时间复杂度
- **删除操作**：O(1)时间复杂度
- **查找操作**：O(1)时间复杂度
- **遍历操作**：O(n)时间复杂度
- **线程安全**：所有操作都是同步的

## ConcurrentHashMap（并发安全的哈希表实现）

### ConcurrentHashMap特点
- **基于哈希表**：底层使用分段锁或CAS实现
- **线程安全**：支持高并发访问
- **无序性**：元素没有顺序
- **高性能**：比Hashtable性能更高

### ConcurrentHashMap实现原理
```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
        implements ConcurrentMap<K,V>, Serializable {
    
    static final int DEFAULT_INITIAL_CAPACITY = 16;
    static final int MAXIMUM_CAPACITY = 1 << 30;
    static final int DEFAULT_CONCURRENCY_LEVEL = 16;
    static final float LOAD_FACTOR = 0.75f;
    static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;
    
    transient volatile Node<K,V>[] table;
    private transient volatile Node<K,V>[] nextTable;
    private transient volatile int sizeCtl;
    private transient volatile int transferIndex;
    private transient volatile int cellsBusy;
    private transient volatile CounterCell[] counterCells;
}
```

### ConcurrentHashMap核心方法
```java
public class ConcurrentHashMapExample {
    public void demonstrateConcurrentHashMap() {
        // 创建ConcurrentHashMap
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        
        // 添加键值对
        map.put("Java", 1);
        map.put("Python", 2);
        map.put("C++", 3);
        
        // 获取值
        Integer value = map.get("Java");
        
        // 检查是否包含键
        boolean containsKey = map.containsKey("Java");
        
        // 删除键值对
        Integer removed = map.remove("Java");
        
        // 原子操作
        Integer oldValue = map.putIfAbsent("Java", 1);
        boolean replaced = map.replace("Java", 1, 2);
        Integer removedValue = map.remove("Java", 1);
        
        // 获取大小
        int size = map.size();
        
        // 检查是否为空
        boolean isEmpty = map.isEmpty();
        
        // 清空映射
        map.clear();
    }
}
```

### ConcurrentHashMap性能特点
- **添加操作**：O(1)时间复杂度
- **删除操作**：O(1)时间复杂度
- **查找操作**：O(1)时间复杂度
- **遍历操作**：O(n)时间复杂度
- **线程安全**：支持高并发访问

## 五种Map实现对比

### 性能对比
| 操作 | HashMap | TreeMap | LinkedHashMap | Hashtable | ConcurrentHashMap |
|------|---------|---------|---------------|-----------|-------------------|
| 添加操作 | O(1) | O(log n) | O(1) | O(1) | O(1) |
| 删除操作 | O(1) | O(log n) | O(1) | O(1) | O(1) |
| 查找操作 | O(1) | O(log n) | O(1) | O(1) | O(1) |
| 遍历操作 | O(n) | O(n) | O(n) | O(n) | O(n) |
| 线程安全 | 否 | 否 | 否 | 是 | 是 |

### 内存使用对比
| 特性 | HashMap | TreeMap | LinkedHashMap | Hashtable | ConcurrentHashMap |
|------|---------|---------|---------------|-----------|-------------------|
| 内存使用 | 中等 | 高 | 高 | 中等 | 高 |
| 额外开销 | 小 | 大 | 大 | 小 | 大 |
| 缓存友好 | 是 | 否 | 是 | 是 | 是 |

### 使用场景对比
| 场景 | 推荐实现 | 原因 |
|------|----------|------|
| 快速查找 | HashMap | O(1)查找性能 |
| 需要排序 | TreeMap | 自动排序 |
| 保持插入顺序 | LinkedHashMap | 保持插入顺序 |
| 线程安全需求 | ConcurrentHashMap | 高性能并发安全 |
| 简单线程安全 | Hashtable | 内置同步机制 |
| 内存敏感 | HashMap | 内存使用效率高 |

## 最佳实践

### 选择原则
1. **快速查找**：选择HashMap
2. **需要排序**：选择TreeMap
3. **保持插入顺序**：选择LinkedHashMap
4. **线程安全需求**：选择ConcurrentHashMap
5. **简单线程安全**：选择Hashtable

### 性能优化
1. **合理设置初始容量**：避免频繁扩容
2. **使用合适的负载因子**：平衡空间和时间
3. **避免频繁的插入删除**：批量操作更高效
4. **使用迭代器遍历**：避免使用键集合遍历

### 代码示例
```java
public class MapBestPractices {
    // 预分配容量
    public void preAllocateCapacity() {
        HashMap<String, Integer> map = new HashMap<>(1000);
        // 避免频繁扩容
    }
    
    // 使用迭代器遍历
    public void iterateWithIterator(Map<String, Integer> map) {
        Iterator<Map.Entry<String, Integer>> iterator = map.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<String, Integer> entry = iterator.next();
            // 处理键值对
        }
    }
    
    // 批量操作
    public void batchOperations() {
        HashMap<String, Integer> map1 = new HashMap<>();
        HashMap<String, Integer> map2 = new HashMap<>();
        
        // 批量添加
        map1.putAll(map2);
        
        // 批量删除
        map1.keySet().removeAll(map2.keySet());
    }
}
```

## 总结

HashMap、TreeMap、LinkedHashMap、Hashtable、ConcurrentHashMap各有特点，选择哪种实现需要根据具体的使用场景来决定。在实际开发中，应该根据性能要求、排序需求、线程安全需求、内存使用情况等因素来选择合适的Map实现。

—— 完 ——
