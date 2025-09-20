---
layout: post
title: Java集合接口继承关系
subtitle: Collection框架的接口层次结构与实现关系
date: 2018-08-17
categories: 技术
tags: java 集合 接口 继承
cover: 
---

Java集合框架是Java编程中最重要的部分之一，理解集合接口的继承关系和实现对于掌握Java集合至关重要。

## 集合框架概述

### 集合框架的层次结构
Java集合框架主要分为两个分支：
- **Collection接口**：单列集合的根接口
- **Map接口**：双列集合的根接口

### 集合框架的核心接口
- **Collection**：单列集合的根接口
- **List**：有序、可重复的集合
- **Set**：无序、不可重复的集合
- **Map**：键值对映射的集合

## Collection接口

### Collection接口定义
```java
public interface Collection<E> extends Iterable<E> {
    // 基本操作
    int size();
    boolean isEmpty();
    boolean contains(Object o);
    boolean add(E e);
    boolean remove(Object o);
    boolean containsAll(Collection<?> c);
    boolean addAll(Collection<? extends E> c);
    boolean removeAll(Collection<?> c);
    boolean retainAll(Collection<?> c);
    void clear();
    
    // 数组操作
    Object[] toArray();
    <T> T[] toArray(T[] a);
    
    // 迭代器
    Iterator<E> iterator();
}
```

### Collection接口的特点
- **单列集合**：存储单个元素
- **可迭代**：实现了Iterable接口
- **泛型支持**：支持泛型
- **基本操作**：提供基本的增删改查操作

## List接口

### List接口定义
```java
public interface List<E> extends Collection<E> {
    // 位置访问
    E get(int index);
    E set(int index, E element);
    void add(int index, E element);
    E remove(int index);
    
    // 搜索操作
    int indexOf(Object o);
    int lastIndexOf(Object o);
    
    // 范围操作
    List<E> subList(int fromIndex, int toIndex);
    
    // 列表迭代器
    ListIterator<E> listIterator();
    ListIterator<E> listIterator(int index);
}
```

### List接口的特点
- **有序**：元素有顺序
- **可重复**：允许重复元素
- **位置访问**：可以通过索引访问元素
- **列表迭代器**：提供双向迭代器

### List接口的实现类
- **ArrayList**：基于数组实现
- **LinkedList**：基于链表实现
- **Vector**：线程安全的数组实现
- **Stack**：栈实现

## Set接口

### Set接口定义
```java
public interface Set<E> extends Collection<E> {
    // Set接口没有定义新的方法
    // 继承Collection接口的所有方法
}
```

### Set接口的特点
- **无序**：元素没有顺序
- **不可重复**：不允许重复元素
- **数学集合**：符合数学集合的定义

### Set接口的实现类
- **HashSet**：基于哈希表实现
- **TreeSet**：基于红黑树实现
- **LinkedHashSet**：基于哈希表和链表实现

## Map接口

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

### Map接口的特点
- **键值对**：存储键值对映射
- **键唯一**：键不能重复
- **值可重复**：值可以重复
- **视图操作**：提供键、值、条目的视图

### Map接口的实现类
- **HashMap**：基于哈希表实现
- **TreeMap**：基于红黑树实现
- **LinkedHashMap**：基于哈希表和链表实现
- **Hashtable**：线程安全的哈希表实现
- **ConcurrentHashMap**：并发安全的哈希表实现

## 接口继承关系图

### Collection分支
```
Collection<E>
    ├── List<E>
    │   ├── ArrayList<E>
    │   ├── LinkedList<E>
    │   ├── Vector<E>
    │   └── Stack<E>
    └── Set<E>
        ├── HashSet<E>
        ├── TreeSet<E>
        └── LinkedHashSet<E>
```

### Map分支
```
Map<K,V>
    ├── HashMap<K,V>
    ├── TreeMap<K,V>
    ├── LinkedHashMap<K,V>
    ├── Hashtable<K,V>
    └── ConcurrentHashMap<K,V>
```

## 接口实现关系

### List接口实现
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    // ArrayList实现
}

public class LinkedList<E> extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, java.io.Serializable {
    // LinkedList实现
}

public class Vector<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    // Vector实现
}
```

### Set接口实现
```java
public class HashSet<E> extends AbstractSet<E>
        implements Set<E>, Cloneable, java.io.Serializable {
    // HashSet实现
}

public class TreeSet<E> extends AbstractSet<E>
        implements NavigableSet<E>, Cloneable, java.io.Serializable {
    // TreeSet实现
}

public class LinkedHashSet<E> extends HashSet<E>
        implements Set<E>, Cloneable, java.io.Serializable {
    // LinkedHashSet实现
}
```

### Map接口实现
```java
public class HashMap<K,V> extends AbstractMap<K,V>
        implements Map<K,V>, Cloneable, java.io.Serializable {
    // HashMap实现
}

public class TreeMap<K,V> extends AbstractMap<K,V>
        implements NavigableMap<K,V>, Cloneable, java.io.Serializable {
    // TreeMap实现
}

public class LinkedHashMap<K,V> extends HashMap<K,V>
        implements Map<K,V> {
    // LinkedHashMap实现
}
```

## 抽象类的作用

### AbstractCollection
- **作用**：为Collection接口提供默认实现
- **特点**：减少实现类的工作量
- **方法**：提供基本的集合操作实现

### AbstractList
- **作用**：为List接口提供默认实现
- **特点**：基于随机访问的列表实现
- **方法**：提供基本的列表操作实现

### AbstractSet
- **作用**：为Set接口提供默认实现
- **特点**：基于集合的数学定义
- **方法**：提供基本的集合操作实现

### AbstractMap
- **作用**：为Map接口提供默认实现
- **特点**：基于键值对映射
- **方法**：提供基本的映射操作实现

## 接口设计原则

### 单一职责原则
- **Collection**：负责单列集合的基本操作
- **List**：负责有序集合的操作
- **Set**：负责无序集合的操作
- **Map**：负责键值对映射的操作

### 开闭原则
- **对扩展开放**：可以添加新的实现类
- **对修改关闭**：接口定义稳定不变

### 里氏替换原则
- **子类替换**：子类可以替换父类使用
- **多态性**：利用多态性实现不同行为

## 使用建议

### 接口选择
1. **需要有序**：选择List接口
2. **需要唯一**：选择Set接口
3. **需要映射**：选择Map接口
4. **需要线程安全**：选择同步实现类

### 实现类选择
1. **频繁随机访问**：选择ArrayList
2. **频繁插入删除**：选择LinkedList
3. **需要排序**：选择TreeSet/TreeMap
4. **需要保持插入顺序**：选择LinkedHashSet/LinkedHashMap

### 性能考虑
1. **时间复杂度**：了解各操作的时间复杂度
2. **空间复杂度**：考虑内存使用情况
3. **线程安全**：根据并发需求选择实现类

## 总结

Java集合框架的接口继承关系体现了良好的面向对象设计原则，理解这些接口的特点和实现关系对于正确使用Java集合至关重要。在实际开发中，应该根据具体需求选择合适的接口和实现类。

—— 完 ——
