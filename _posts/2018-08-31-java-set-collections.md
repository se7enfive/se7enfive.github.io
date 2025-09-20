---
layout: post
title: Java Set集合详解
subtitle: HashSet、TreeSet、LinkedHashSet的特点与使用场景
date: 2018-08-31
categories: 技术
tags: java set 集合 数据结构
cover: 
---

Set是Java集合框架中用于存储唯一元素的接口，理解HashSet、TreeSet、LinkedHashSet的特点和使用场景对于Java开发至关重要。

## Set接口概述

### Set接口特点
- **唯一性**：不允许重复元素
- **无序性**：元素没有顺序（除了LinkedHashSet）
- **数学集合**：符合数学集合的定义
- **继承Collection**：继承Collection接口的所有方法

### Set接口定义
```java
public interface Set<E> extends Collection<E> {
    // Set接口没有定义新的方法
    // 继承Collection接口的所有方法
    // 但要求实现类保证元素的唯一性
}
```

### Set接口的实现类
- **HashSet**：基于哈希表实现
- **TreeSet**：基于红黑树实现
- **LinkedHashSet**：基于哈希表和链表实现

## HashSet（哈希表实现）

### HashSet特点
- **基于哈希表**：底层使用HashMap实现
- **无序性**：元素没有顺序
- **高效查找**：O(1)时间复杂度的查找
- **非线程安全**：不是线程安全的

### HashSet实现原理
```java
public class HashSet<E> extends AbstractSet<E>
        implements Set<E>, Cloneable, java.io.Serializable {
    
    private transient HashMap<E,Object> map;
    private static final Object PRESENT = new Object();
    
    public HashSet() {
        map = new HashMap<>();
    }
    
    public HashSet(Collection<? extends E> c) {
        map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
        addAll(c);
    }
    
    public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
    }
    
    public HashSet(int initialCapacity) {
        map = new HashMap<>(initialCapacity);
    }
}
```

### HashSet核心方法
```java
public class HashSetExample {
    public void demonstrateHashSet() {
        // 创建HashSet
        HashSet<String> set = new HashSet<>();
        
        // 添加元素
        set.add("Java");
        set.add("Python");
        set.add("C++");
        set.add("Java"); // 重复元素，不会被添加
        
        // 检查是否包含
        boolean contains = set.contains("Java");
        
        // 删除元素
        boolean removed = set.remove("Python");
        
        // 获取大小
        int size = set.size();
        
        // 检查是否为空
        boolean isEmpty = set.isEmpty();
        
        // 清空集合
        set.clear();
    }
}
```

### HashSet性能特点
- **添加操作**：O(1)时间复杂度
- **删除操作**：O(1)时间复杂度
- **查找操作**：O(1)时间复杂度
- **遍历操作**：O(n)时间复杂度

## TreeSet（红黑树实现）

### TreeSet特点
- **基于红黑树**：底层使用TreeMap实现
- **有序性**：元素按照自然顺序或比较器顺序排列
- **高效查找**：O(log n)时间复杂度的查找
- **非线程安全**：不是线程安全的

### TreeSet实现原理
```java
public class TreeSet<E> extends AbstractSet<E>
        implements NavigableSet<E>, Cloneable, java.io.Serializable {
    
    private transient NavigableMap<E,Object> m;
    private static final Object PRESENT = new Object();
    
    public TreeSet() {
        this(new TreeMap<E,Object>());
    }
    
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }
    
    public TreeSet(Collection<? extends E> c) {
        this();
        addAll(c);
    }
    
    public TreeSet(SortedSet<E> s) {
        this(s.comparator());
        addAll(s);
    }
}
```

### TreeSet核心方法
```java
public class TreeSetExample {
    public void demonstrateTreeSet() {
        // 创建TreeSet
        TreeSet<String> set = new TreeSet<>();
        
        // 添加元素
        set.add("Java");
        set.add("Python");
        set.add("C++");
        set.add("JavaScript");
        
        // 获取第一个元素
        String first = set.first();
        
        // 获取最后一个元素
        String last = set.last();
        
        // 获取小于指定元素的最大元素
        String lower = set.lower("Python");
        
        // 获取大于指定元素的最小元素
        String higher = set.higher("Python");
        
        // 获取子集
        SortedSet<String> subSet = set.subSet("C++", "Python");
        
        // 获取头部子集
        SortedSet<String> headSet = set.headSet("Python");
        
        // 获取尾部子集
        SortedSet<String> tailSet = set.tailSet("Python");
    }
}
```

### TreeSet性能特点
- **添加操作**：O(log n)时间复杂度
- **删除操作**：O(log n)时间复杂度
- **查找操作**：O(log n)时间复杂度
- **遍历操作**：O(n)时间复杂度

## LinkedHashSet（哈希表+链表实现）

### LinkedHashSet特点
- **基于哈希表和链表**：底层使用LinkedHashMap实现
- **有序性**：保持插入顺序
- **高效查找**：O(1)时间复杂度的查找
- **非线程安全**：不是线程安全的

### LinkedHashSet实现原理
```java
public class LinkedHashSet<E> extends HashSet<E>
        implements Set<E>, Cloneable, java.io.Serializable {
    
    public LinkedHashSet() {
        super(16, .75f);
    }
    
    public LinkedHashSet(Collection<? extends E> c) {
        super(Math.max(2*c.size(), 11), .75f);
        addAll(c);
    }
    
    public LinkedHashSet(int initialCapacity) {
        super(initialCapacity, .75f);
    }
    
    public LinkedHashSet(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor);
    }
}
```

### LinkedHashSet核心方法
```java
public class LinkedHashSetExample {
    public void demonstrateLinkedHashSet() {
        // 创建LinkedHashSet
        LinkedHashSet<String> set = new LinkedHashSet<>();
        
        // 添加元素
        set.add("Java");
        set.add("Python");
        set.add("C++");
        set.add("JavaScript");
        
        // 遍历元素（保持插入顺序）
        for (String element : set) {
            System.out.println(element);
        }
        
        // 其他操作与HashSet相同
        boolean contains = set.contains("Java");
        boolean removed = set.remove("Python");
        int size = set.size();
    }
}
```

### LinkedHashSet性能特点
- **添加操作**：O(1)时间复杂度
- **删除操作**：O(1)时间复杂度
- **查找操作**：O(1)时间复杂度
- **遍历操作**：O(n)时间复杂度

## 三种Set实现对比

### 性能对比
| 操作 | HashSet | TreeSet | LinkedHashSet |
|------|---------|---------|---------------|
| 添加操作 | O(1) | O(log n) | O(1) |
| 删除操作 | O(1) | O(log n) | O(1) |
| 查找操作 | O(1) | O(log n) | O(1) |
| 遍历操作 | O(n) | O(n) | O(n) |
| 有序性 | 无 | 有 | 有（插入顺序） |

### 内存使用对比
| 特性 | HashSet | TreeSet | LinkedHashSet |
|------|---------|---------|---------------|
| 内存使用 | 中等 | 高 | 高 |
| 额外开销 | 小 | 大 | 大 |
| 缓存友好 | 是 | 否 | 是 |

### 使用场景对比
| 场景 | 推荐实现 | 原因 |
|------|----------|------|
| 快速查找 | HashSet | O(1)查找性能 |
| 需要排序 | TreeSet | 自动排序 |
| 保持插入顺序 | LinkedHashSet | 保持插入顺序 |
| 内存敏感 | HashSet | 内存使用效率高 |
| 范围查询 | TreeSet | 支持范围操作 |

## 自定义比较器

### 自然排序
```java
public class NaturalOrderExample {
    public void demonstrateNaturalOrder() {
        // 使用自然排序
        TreeSet<String> set = new TreeSet<>();
        set.add("Java");
        set.add("Python");
        set.add("C++");
        
        // 输出：C++, Java, Python
        for (String element : set) {
            System.out.println(element);
        }
    }
}
```

### 自定义比较器
```java
public class CustomComparatorExample {
    public void demonstrateCustomComparator() {
        // 使用自定义比较器（按长度排序）
        TreeSet<String> set = new TreeSet<>(new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return Integer.compare(o1.length(), o2.length());
            }
        });
        
        set.add("Java");
        set.add("Python");
        set.add("C++");
        
        // 输出：C++, Java, Python
        for (String element : set) {
            System.out.println(element);
        }
    }
}
```

### 对象排序
```java
public class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // getter和setter方法
    
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}

public class PersonSetExample {
    public void demonstratePersonSet() {
        // 使用自定义比较器
        TreeSet<Person> set = new TreeSet<>(new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                return Integer.compare(o1.getAge(), o2.getAge());
            }
        });
        
        set.add(new Person("Alice", 25));
        set.add(new Person("Bob", 30));
        set.add(new Person("Charlie", 20));
        
        // 按年龄排序输出
        for (Person person : set) {
            System.out.println(person);
        }
    }
}
```

## 最佳实践

### 选择原则
1. **快速查找**：选择HashSet
2. **需要排序**：选择TreeSet
3. **保持插入顺序**：选择LinkedHashSet
4. **内存敏感**：选择HashSet

### 性能优化
1. **合理设置初始容量**：避免频繁扩容
2. **使用合适的比较器**：避免复杂的比较逻辑
3. **避免频繁的插入删除**：批量操作更高效
4. **使用迭代器遍历**：避免使用索引遍历

### 代码示例
```java
public class SetBestPractices {
    // 预分配容量
    public void preAllocateCapacity() {
        HashSet<String> set = new HashSet<>(1000);
        // 避免频繁扩容
    }
    
    // 使用迭代器遍历
    public void iterateWithIterator(Set<String> set) {
        Iterator<String> iterator = set.iterator();
        while (iterator.hasNext()) {
            String element = iterator.next();
            // 处理元素
        }
    }
    
    // 批量操作
    public void batchOperations() {
        HashSet<String> set1 = new HashSet<>();
        HashSet<String> set2 = new HashSet<>();
        
        // 批量添加
        set1.addAll(set2);
        
        // 批量删除
        set1.removeAll(set2);
        
        // 保留交集
        set1.retainAll(set2);
    }
}
```

## 总结

HashSet、TreeSet、LinkedHashSet各有特点，选择哪种实现需要根据具体的使用场景来决定。在实际开发中，应该根据性能要求、排序需求、内存使用情况等因素来选择合适的Set实现。

—— 完 ——
