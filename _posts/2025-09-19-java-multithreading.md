---
layout: post
title: Java多线程与并发编程
subtitle: 线程基础、锁机制与并发工具
date: 2025-09-19
categories: 技术
tags: java 多线程 并发 锁机制
cover: 
---

本文详细介绍Java多线程与并发编程的核心概念，包括线程基础、锁机制、同步工具等。

## 线程基础

### 线程状态
- **NEW**：新建状态
- **RUNNABLE**：可运行状态
- **BLOCKED**：阻塞状态
- **WAITING**：等待状态
- **TIMED_WAITING**：超时等待状态
- **TERMINATED**：终止状态

### 线程创建方式
1. **继承Thread类**
2. **实现Runnable接口**
3. **实现Callable接口**
4. **使用线程池**

### 线程生命周期
```
NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → TERMINATED
```

## 锁机制

### synchronized关键字
- **作用**：保证同一时刻只有一个线程执行同步代码
- **使用方式**：
  - 同步方法
  - 同步代码块
- **特点**：可重入、不可中断、非公平

### Lock接口
- **实现类**：ReentrantLock、ReadWriteLock
- **特点**：可重入、可中断、可公平
- **优势**：
  - 可以知道是否成功获取锁
  - 可以提高多线程读操作效率
  - 可以实现读写锁

### 锁升级过程
1. **偏向锁**：只有一个线程访问时使用
2. **轻量级锁**：多线程竞争不激烈时使用
3. **重量级锁**：多线程竞争激烈时使用

## 同步工具

### volatile关键字
- **作用**：保证变量可见性，禁止指令重排序
- **特点**：轻量级同步机制
- **使用场景**：状态标志、双重检查锁定

### Atomic类
- **作用**：原子操作，线程安全
- **常用类**：AtomicInteger、AtomicLong、AtomicReference
- **特点**：无锁编程，性能较高

### 线程间通信

#### wait()和notify()
- **wait()**：使当前线程等待
- **notify()**：唤醒一个等待线程
- **notifyAll()**：唤醒所有等待线程
- **注意**：必须在synchronized块中使用

#### 生产者消费者模式
```java
// 使用wait/notify实现
public class ProducerConsumer {
    private final Object lock = new Object();
    private boolean hasData = false;
    
    public void produce() {
        synchronized (lock) {
            while (hasData) {
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
            // 生产数据
            hasData = true;
            lock.notify();
        }
    }
    
    public void consume() {
        synchronized (lock) {
            while (!hasData) {
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
            // 消费数据
            hasData = false;
            lock.notify();
        }
    }
}
```

## 线程池

### 线程池优势
1. **降低资源消耗**：重复利用已创建的线程
2. **提高响应速度**：任务到达时无需等待线程创建
3. **提高线程可管理性**：统一分配、调优和监控

### 线程池核心参数
- **corePoolSize**：核心线程数
- **maximumPoolSize**：最大线程数
- **keepAliveTime**：线程存活时间
- **workQueue**：工作队列
- **threadFactory**：线程工厂
- **handler**：拒绝策略

### 拒绝策略
1. **AbortPolicy**：直接抛出异常
2. **CallerRunsPolicy**：调用者线程执行
3. **DiscardOldestPolicy**：丢弃最老的任务
4. **DiscardPolicy**：静默丢弃

### 常用线程池
- **FixedThreadPool**：固定大小线程池
- **CachedThreadPool**：缓存线程池
- **ScheduledThreadPool**：定时任务线程池
- **SingleThreadExecutor**：单线程池

## 并发工具类

### CountDownLatch
- **作用**：等待多个线程完成
- **使用场景**：主线程等待子线程完成

### CyclicBarrier
- **作用**：多个线程相互等待
- **特点**：可重复使用

### Semaphore
- **作用**：控制同时访问资源的线程数
- **使用场景**：限流

### Exchanger
- **作用**：两个线程交换数据
- **使用场景**：数据交换

## 线程安全

### 线程安全级别
1. **不可变对象**：String、Integer等
2. **绝对线程安全**：Vector、Hashtable等
3. **相对线程安全**：ArrayList、HashMap等
4. **线程兼容**：需要外部同步
5. **线程对立**：无论是否同步都不安全

### 线程安全实现方式
1. **互斥同步**：synchronized、Lock
2. **非阻塞同步**：CAS操作
3. **无同步方案**：ThreadLocal、不可变对象

## 性能优化建议

1. **减少锁的持有时间**
2. **减少锁的粒度**
3. **使用读写锁**
4. **避免锁的嵌套**
5. **使用无锁编程**
6. **合理使用线程池**

## 常见问题

### 死锁
- **原因**：多个线程相互等待对方释放锁
- **避免**：按顺序获取锁、使用超时机制

### 活锁
- **原因**：线程不断改变状态，但无法继续执行
- **避免**：引入随机性、使用超时机制

### 饥饿
- **原因**：线程长时间无法获得资源
- **避免**：公平锁、优先级调度

## 总结

Java多线程与并发编程是Java开发的重要技能，需要深入理解线程基础、锁机制、同步工具等核心概念。建议通过实际项目练习，掌握并发编程的最佳实践。

—— 完 ——
