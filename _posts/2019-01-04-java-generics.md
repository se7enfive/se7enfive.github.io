---
layout: post
title: Java泛型详解
subtitle: 泛型概念、通配符、类型擦除与最佳实践
date: 2019-01-04
categories: 技术
tags: java 泛型 类型安全 编程基础
cover: 
---

Java泛型是Java 5引入的重要特性，它提供了类型安全和代码重用的机制。理解泛型的概念、使用方法和最佳实践对于编写高质量的Java程序至关重要。

## 泛型概述

### 泛型的概念
泛型是参数化类型，它允许在定义类、接口和方法时使用类型参数，在实例化时指定具体的类型。

### 泛型的作用
- **类型安全**：在编译时检查类型安全
- **代码重用**：提高代码的重用性
- **消除强制转换**：避免运行时类型转换
- **提高可读性**：代码更加清晰易读

### 泛型的基本语法
```java
public class GenericClass<T> {
    private T data;
    
    public void setData(T data) {
        this.data = data;
    }
    
    public T getData() {
        return data;
    }
}
```

## 泛型的使用

### 1. 泛型类
```java
public class Box<T> {
    private T content;
    
    public void setContent(T content) {
        this.content = content;
    }
    
    public T getContent() {
        return content;
    }
}

// 使用泛型类
Box<String> stringBox = new Box<>();
stringBox.setContent("Hello");
String content = stringBox.getContent();
```

### 2. 泛型接口
```java
public interface List<T> {
    void add(T item);
    T get(int index);
    int size();
}

// 实现泛型接口
public class ArrayList<T> implements List<T> {
    private Object[] elements;
    private int size;
    
    @Override
    public void add(T item) {
        // 实现添加逻辑
    }
    
    @Override
    public T get(int index) {
        // 实现获取逻辑
        return (T) elements[index];
    }
    
    @Override
    public int size() {
        return size;
    }
}
```

### 3. 泛型方法
```java
public class GenericMethod {
    public static <T> T getFirst(T[] array) {
        if (array.length > 0) {
            return array[0];
        }
        return null;
    }
    
    public static <T> void printArray(T[] array) {
        for (T item : array) {
            System.out.println(item);
        }
    }
}
```

## 通配符

### 1. 无界通配符
```java
public class WildcardExample {
    public void printList(List<?> list) {
        for (Object item : list) {
            System.out.println(item);
        }
    }
}
```

### 2. 上界通配符
```java
public class UpperBoundWildcard {
    public void printNumbers(List<? extends Number> numbers) {
        for (Number number : numbers) {
            System.out.println(number.doubleValue());
        }
    }
}
```

### 3. 下界通配符
```java
public class LowerBoundWildcard {
    public void addNumbers(List<? super Integer> numbers) {
        numbers.add(1);
        numbers.add(2);
        numbers.add(3);
    }
}
```

## 类型擦除

### 类型擦除的概念
Java泛型在编译时进行类型检查，在运行时擦除类型信息，这是为了保持向后兼容性。

### 类型擦除的影响
```java
public class TypeErasureExample {
    public void demonstrateTypeErasure() {
        List<String> stringList = new ArrayList<>();
        List<Integer> intList = new ArrayList<>();
        
        // 编译时类型不同
        // 运行时类型相同（都是List）
        System.out.println(stringList.getClass() == intList.getClass()); // true
    }
}
```

### 类型擦除的限制
```java
public class TypeErasureLimitations {
    // 不能创建泛型数组
    // T[] array = new T[10]; // 编译错误
    
    // 不能使用instanceof检查泛型类型
    public void checkType(Object obj) {
        // if (obj instanceof List<String>) { // 编译错误
        if (obj instanceof List) {
            // 正确的检查方式
        }
    }
}
```

## 泛型的实际应用

### 1. 集合框架
```java
public class CollectionExample {
    public void useCollections() {
        // 类型安全的集合
        List<String> stringList = new ArrayList<>();
        stringList.add("Hello");
        stringList.add("World");
        
        // 编译时类型检查
        // stringList.add(123); // 编译错误
        
        // 无需强制转换
        String first = stringList.get(0);
    }
}
```

### 2. 比较器
```java
public class ComparatorExample {
    public void useComparator() {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        
        // 使用泛型比较器
        Collections.sort(names, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o1.compareTo(o2);
            }
        });
    }
}
```

### 3. 工厂模式
```java
public class FactoryExample {
    public static <T> T createInstance(Class<T> clazz) {
        try {
            return clazz.newInstance();
        } catch (Exception e) {
            throw new RuntimeException("创建实例失败", e);
        }
    }
    
    public void useFactory() {
        String str = createInstance(String.class);
        Integer num = createInstance(Integer.class);
    }
}
```

## 泛型的最佳实践

### 1. 命名约定
```java
public class NamingConvention {
    // 使用单个大写字母作为类型参数名
    public class GenericClass<T> { }
    
    // 使用有意义的名称
    public class GenericClass<ElementType> { }
    
    // 避免使用Object作为类型参数
    public class GenericClass<Object> { } // 不推荐
}
```

### 2. 边界使用
```java
public class BoundsExample {
    // 使用上界限制类型参数
    public class NumberBox<T extends Number> {
        private T number;
        
        public double getDoubleValue() {
            return number.doubleValue();
        }
    }
    
    // 使用多个边界
    public class ComparableBox<T extends Number & Comparable<T>> {
        private T value;
        
        public int compareTo(ComparableBox<T> other) {
            return value.compareTo(other.value);
        }
    }
}
```

### 3. 通配符使用
```java
public class WildcardBestPractices {
    // 使用通配符提高灵活性
    public void processList(List<? extends Number> numbers) {
        for (Number number : numbers) {
            System.out.println(number.doubleValue());
        }
    }
    
    // 使用通配符进行类型安全的操作
    public void addNumbers(List<? super Integer> numbers) {
        numbers.add(1);
        numbers.add(2);
    }
}
```

## 泛型的限制和注意事项

### 1. 不能实例化泛型类型
```java
public class InstantiationLimitation {
    public void demonstrateLimitation() {
        // T obj = new T(); // 编译错误
        // T[] array = new T[10]; // 编译错误
    }
}
```

### 2. 不能使用基本类型
```java
public class PrimitiveTypeLimitation {
    public void demonstrateLimitation() {
        // List<int> intList = new ArrayList<>(); // 编译错误
        List<Integer> intList = new ArrayList<>(); // 正确
    }
}
```

### 3. 不能重载泛型方法
```java
public class OverloadingLimitation {
    // 这两个方法在类型擦除后是相同的
    // public void method(List<String> list) { }
    // public void method(List<Integer> list) { } // 编译错误
}
```

## 总结

Java泛型是强大的类型安全机制，它提供了编译时类型检查和代码重用能力。通过合理使用泛型、通配符和边界，可以编写出更加安全、灵活和可维护的代码。

—— 完 ——
