---
layout: post
title: Java List集合详解
subtitle: ArrayList、LinkedList、Vector的特点与使用场景
date: 2018-08-24
categories: 技术
tags: java list 集合 数据结构
cover: 
---

List是Java集合框架中最重要的接口之一，理解ArrayList、LinkedList、Vector的特点和使用场景对于Java开发至关重要。

## List接口概述

### List接口特点
- **有序**：元素有顺序，可以通过索引访问
- **可重复**：允许存储重复元素
- **位置访问**：支持通过索引进行随机访问
- **双向迭代**：支持双向迭代器

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

## ArrayList（数组实现）

### ArrayList特点
- **基于数组**：底层使用数组实现
- **随机访问**：支持O(1)时间复杂度的随机访问
- **动态扩容**：自动扩容，默认初始容量为10
- **非线程安全**：不是线程安全的

### ArrayList实现原理
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    
    private static final int DEFAULT_CAPACITY = 10;
    private static final Object[] EMPTY_ELEMENTDATA = {};
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    
    transient Object[] elementData; // 存储元素的数组
    private int size; // 元素个数
    
    // 构造方法
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
    
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
        }
    }
}
```

### ArrayList核心方法
```java
public class ArrayListExample {
    public void demonstrateArrayList() {
        // 创建ArrayList
        ArrayList<String> list = new ArrayList<>();
        
        // 添加元素
        list.add("Java");
        list.add("Python");
        list.add("C++");
        
        // 获取元素
        String element = list.get(0); // "Java"
        
        // 设置元素
        list.set(1, "JavaScript");
        
        // 删除元素
        list.remove(0);
        
        // 获取大小
        int size = list.size();
        
        // 检查是否包含
        boolean contains = list.contains("Python");
        
        // 获取索引
        int index = list.indexOf("C++");
    }
}
```

### ArrayList扩容机制
```java
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 扩容1.5倍
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

### ArrayList性能特点
- **随机访问**：O(1)时间复杂度
- **末尾插入**：O(1)时间复杂度
- **中间插入**：O(n)时间复杂度
- **删除操作**：O(n)时间复杂度

## LinkedList（链表实现）

### LinkedList特点
- **基于链表**：底层使用双向链表实现
- **顺序访问**：需要顺序访问元素
- **高效插入删除**：在已知位置插入删除效率高
- **非线程安全**：不是线程安全的

### LinkedList实现原理
```java
public class LinkedList<E> extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, java.io.Serializable {
    
    transient int size = 0;
    transient Node<E> first; // 头节点
    transient Node<E> last;  // 尾节点
    
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;
        
        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
}
```

### LinkedList核心方法
```java
public class LinkedListExample {
    public void demonstrateLinkedList() {
        // 创建LinkedList
        LinkedList<String> list = new LinkedList<>();
        
        // 添加元素
        list.add("Java");
        list.add("Python");
        list.add("C++");
        
        // 在头部添加
        list.addFirst("Go");
        
        // 在尾部添加
        list.addLast("Rust");
        
        // 获取头部元素
        String first = list.getFirst();
        
        // 获取尾部元素
        String last = list.getLast();
        
        // 删除头部元素
        String removed = list.removeFirst();
        
        // 删除尾部元素
        String removedLast = list.removeLast();
    }
}
```

### LinkedList性能特点
- **随机访问**：O(n)时间复杂度
- **头部插入删除**：O(1)时间复杂度
- **尾部插入删除**：O(1)时间复杂度
- **中间插入删除**：O(n)时间复杂度

## Vector（线程安全的数组实现）

### Vector特点
- **基于数组**：底层使用数组实现
- **线程安全**：所有方法都是同步的
- **动态扩容**：自动扩容，默认扩容2倍
- **性能较低**：由于同步机制，性能较低

### Vector实现原理
```java
public class Vector<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    
    protected Object[] elementData;
    protected int elementCount;
    protected int capacityIncrement;
    
    public Vector() {
        this(10);
    }
    
    public Vector(int initialCapacity) {
        this(initialCapacity, 0);
    }
    
    public Vector(int initialCapacity, int capacityIncrement) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
        this.elementData = new Object[initialCapacity];
        this.capacityIncrement = capacityIncrement;
    }
}
```

### Vector核心方法
```java
public class VectorExample {
    public void demonstrateVector() {
        // 创建Vector
        Vector<String> vector = new Vector<>();
        
        // 添加元素
        vector.add("Java");
        vector.add("Python");
        vector.add("C++");
        
        // 获取元素
        String element = vector.get(0);
        
        // 设置元素
        vector.set(1, "JavaScript");
        
        // 删除元素
        vector.remove(0);
        
        // 获取大小
        int size = vector.size();
        
        // 检查是否包含
        boolean contains = vector.contains("Python");
    }
}
```

### Vector性能特点
- **随机访问**：O(1)时间复杂度
- **插入删除**：O(n)时间复杂度
- **线程安全**：所有操作都是同步的
- **性能较低**：由于同步机制，性能较低

## 三种List实现对比

### 性能对比
| 操作 | ArrayList | LinkedList | Vector |
|------|-----------|------------|--------|
| 随机访问 | O(1) | O(n) | O(1) |
| 头部插入 | O(n) | O(1) | O(n) |
| 尾部插入 | O(1) | O(1) | O(1) |
| 中间插入 | O(n) | O(n) | O(n) |
| 删除操作 | O(n) | O(n) | O(n) |
| 线程安全 | 否 | 否 | 是 |

### 内存使用对比
| 特性 | ArrayList | LinkedList | Vector |
|------|-----------|------------|--------|
| 内存连续 | 是 | 否 | 是 |
| 额外开销 | 小 | 大 | 小 |
| 缓存友好 | 是 | 否 | 是 |

### 使用场景对比
| 场景 | 推荐实现 | 原因 |
|------|----------|------|
| 频繁随机访问 | ArrayList | O(1)随机访问 |
| 频繁插入删除 | LinkedList | O(1)头部尾部操作 |
| 线程安全需求 | Vector | 内置同步机制 |
| 内存敏感 | ArrayList | 内存使用效率高 |
| 缓存友好 | ArrayList | 连续内存布局 |

## 最佳实践

### 选择原则
1. **随机访问频繁**：选择ArrayList
2. **插入删除频繁**：选择LinkedList
3. **线程安全需求**：选择Vector或Collections.synchronizedList()
4. **内存敏感**：选择ArrayList

### 性能优化
1. **预分配容量**：使用带初始容量的构造方法
2. **避免频繁扩容**：合理设置初始容量
3. **使用迭代器**：避免使用索引遍历
4. **批量操作**：使用addAll()等方法

### 代码示例
```java
public class ListBestPractices {
    // 预分配容量
    public void preAllocateCapacity() {
        ArrayList<String> list = new ArrayList<>(1000);
        // 避免频繁扩容
    }
    
    // 使用迭代器遍历
    public void iterateWithIterator(List<String> list) {
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            String element = iterator.next();
            // 处理元素
        }
    }
    
    // 批量操作
    public void batchOperations() {
        ArrayList<String> list1 = new ArrayList<>();
        ArrayList<String> list2 = new ArrayList<>();
        
        // 批量添加
        list1.addAll(list2);
        
        // 批量删除
        list1.removeAll(list2);
    }
}
```

## 总结

ArrayList、LinkedList、Vector各有特点，选择哪种实现需要根据具体的使用场景来决定。在实际开发中，应该根据性能要求、线程安全需求、内存使用情况等因素来选择合适的List实现。

—— 完 ——
