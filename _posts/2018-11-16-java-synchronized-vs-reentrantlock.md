---
layout: post
title: synchronized与ReentrantLock对比详解
subtitle: 两种锁机制的区别、使用场景与性能对比
date: 2018-11-16
categories: 技术
tags: java synchronized reentrantlock 锁 并发
cover: 
---

synchronized和ReentrantLock是Java中两种重要的锁机制，理解它们的区别、使用场景和性能特点对于选择合适的同步机制至关重要。

## 两种锁机制概述

### synchronized关键字
- **内置锁**：Java语言内置的同步机制
- **自动管理**：自动获取和释放锁
- **简单易用**：语法简单，使用方便
- **JVM优化**：JVM层面进行优化

### ReentrantLock类
- **显式锁**：需要显式获取和释放锁
- **功能丰富**：提供更多高级功能
- **灵活控制**：可以精确控制锁的获取和释放
- **性能优化**：在某些场景下性能更好

## 基本使用对比

### synchronized使用
```java
public class SynchronizedExample {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
    
    public void incrementWithBlock() {
        synchronized (this) {
            count++;
        }
    }
}
```

### ReentrantLock使用
```java
public class ReentrantLockExample {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();
    
    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

## 功能特性对比

### 1. 可中断性
- **synchronized**：不可中断，一旦获取锁就必须等待
- **ReentrantLock**：可中断，可以使用lockInterruptibly()方法

```java
public class InterruptibilityExample {
    private final ReentrantLock lock = new ReentrantLock();
    
    public void demonstrateInterruptibility() {
        try {
            lock.lockInterruptibly(); // 可中断获取锁
            // 执行操作
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            if (lock.isHeldByCurrentThread()) {
                lock.unlock();
            }
        }
    }
}
```

### 2. 超时获取
- **synchronized**：不支持超时获取
- **ReentrantLock**：支持超时获取

```java
public class TimeoutExample {
    private final ReentrantLock lock = new ReentrantLock();
    
    public void demonstrateTimeout() {
        try {
            if (lock.tryLock(1, TimeUnit.SECONDS)) {
                try {
                    // 执行操作
                } finally {
                    lock.unlock();
                }
            } else {
                System.out.println("获取锁超时");
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### 3. 公平性
- **synchronized**：非公平锁
- **ReentrantLock**：支持公平和非公平锁

```java
public class FairnessExample {
    private final ReentrantLock fairLock = new ReentrantLock(true); // 公平锁
    private final ReentrantLock unfairLock = new ReentrantLock(false); // 非公平锁
}
```

### 4. 条件变量
- **synchronized**：使用wait/notify机制
- **ReentrantLock**：使用Condition接口

```java
public class ConditionExample {
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean conditionMet = false;
    
    public void waitForCondition() throws InterruptedException {
        lock.lock();
        try {
            while (!conditionMet) {
                condition.await();
            }
        } finally {
            lock.unlock();
        }
    }
    
    public void signalCondition() {
        lock.lock();
        try {
            conditionMet = true;
            condition.signal();
        } finally {
            lock.unlock();
        }
    }
}
```

## 性能对比

### 性能特点
- **synchronized**：JVM优化，性能稳定
- **ReentrantLock**：在某些场景下性能更好

### 性能测试
```java
public class PerformanceTest {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();
    
    public void testSynchronized() {
        synchronized (this) {
            count++;
        }
    }
    
    public void testReentrantLock() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

## 使用场景对比

### synchronized适用场景
1. **简单同步**：基本的同步需求
2. **性能要求不高**：对性能要求不高的场景
3. **代码简洁**：希望代码简洁易读
4. **JVM优化**：依赖JVM的优化

### ReentrantLock适用场景
1. **高级功能**：需要可中断、超时等高级功能
2. **性能要求高**：对性能要求较高的场景
3. **复杂同步**：复杂的同步逻辑
4. **条件变量**：需要多个条件变量

## 内存模型对比

### synchronized内存模型
- **获取锁**：刷新工作内存，从主内存读取最新值
- **释放锁**：将工作内存刷新到主内存
- **可见性**：保证可见性
- **有序性**：保证有序性

### ReentrantLock内存模型
- **获取锁**：使用AQS实现，保证可见性
- **释放锁**：使用AQS实现，保证可见性
- **可见性**：保证可见性
- **有序性**：保证有序性

## 锁升级对比

### synchronized锁升级
1. **无锁状态**：初始状态
2. **偏向锁**：只有一个线程访问
3. **轻量级锁**：多个线程竞争但不激烈
4. **重量级锁**：多个线程激烈竞争

### ReentrantLock锁升级
- **无锁升级**：直接使用AQS实现
- **公平性**：支持公平和非公平模式
- **性能**：在某些场景下性能更好

## 最佳实践

### 1. 选择原则
- **简单需求**：使用synchronized
- **复杂需求**：使用ReentrantLock
- **性能要求**：根据实际测试选择
- **功能需求**：根据功能需求选择

### 2. 使用建议
```java
public class LockBestPractices {
    // 简单同步使用synchronized
    public synchronized void simpleMethod() {
        // 简单操作
    }
    
    // 复杂同步使用ReentrantLock
    private final ReentrantLock lock = new ReentrantLock();
    
    public void complexMethod() {
        lock.lock();
        try {
            // 复杂操作
        } finally {
            lock.unlock();
        }
    }
}
```

### 3. 注意事项
- **及时释放锁**：避免死锁
- **异常处理**：在finally块中释放锁
- **性能测试**：根据实际场景测试性能
- **代码可读性**：保持代码的可读性

## 总结

synchronized和ReentrantLock各有特点，选择哪种锁机制需要根据具体的需求来决定。对于简单的同步需求，synchronized是更好的选择；对于复杂的同步需求，ReentrantLock提供了更多的功能和更好的性能。

—— 完 ——
