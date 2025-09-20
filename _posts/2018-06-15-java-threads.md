---
layout: post
title: Java线程基础
subtitle: JVM中的线程管理机制
date: 2018-06-15
categories: 技术
tags: java 线程 jvm
cover: 
---

Java线程是JVM中执行程序的基本单位，理解线程的创建、管理和生命周期对于Java开发至关重要。

## 线程的基本概念

### 什么是线程
线程是程序执行的最小单位，一个进程可以包含多个线程。在Java中，每个线程都有独立的程序计数器、虚拟机栈和本地方法栈。

### 线程与进程的区别
- **进程**：操作系统资源分配的基本单位
- **线程**：CPU调度的基本单位
- **关系**：一个进程可以包含多个线程，线程共享进程的资源

## 线程的创建方式

### 1. 继承Thread类
```java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("线程执行中...");
    }
}

// 使用方式
MyThread thread = new MyThread();
thread.start();
```

### 2. 实现Runnable接口
```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("线程执行中...");
    }
}

// 使用方式
Thread thread = new Thread(new MyRunnable());
thread.start();
```

### 3. 实现Callable接口
```java
public class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        return "线程执行结果";
    }
}

// 使用方式
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<String> future = executor.submit(new MyCallable());
String result = future.get();
```

## 线程状态

### 线程的六种状态
1. **NEW**：新建状态，线程被创建但未启动
2. **RUNNABLE**：可运行状态，线程正在JVM中执行
3. **BLOCKED**：阻塞状态，线程被阻塞等待监视器锁
4. **WAITING**：等待状态，线程等待其他线程的特定动作
5. **TIMED_WAITING**：超时等待状态，线程等待指定时间
6. **TERMINATED**：终止状态，线程执行完毕

### 状态转换图
```
NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → TERMINATED
```

## 线程同步

### synchronized关键字
- **作用**：保证同一时刻只有一个线程执行同步代码
- **使用方式**：
  - 同步方法：`public synchronized void method()`
  - 同步代码块：`synchronized(object) { ... }`

### 锁机制
- **对象锁**：每个对象都有一个锁
- **类锁**：静态方法的锁
- **可重入锁**：同一线程可以多次获得同一把锁

## 线程通信

### wait()和notify()
- **wait()**：使当前线程等待
- **notify()**：唤醒一个等待线程
- **notifyAll()**：唤醒所有等待线程
- **注意**：必须在synchronized块中使用

### 生产者消费者模式
```java
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

## 线程安全

### 什么是线程安全
线程安全是指多线程环境下，程序能够正确执行，不会出现数据竞争和不一致的情况。

### 实现线程安全的方式
1. **同步方法**：使用synchronized关键字
2. **同步代码块**：锁定特定对象
3. **volatile关键字**：保证变量可见性
4. **原子类**：使用AtomicInteger等原子类
5. **不可变对象**：使用final关键字

## 线程池

### 为什么使用线程池
- **降低资源消耗**：重复利用已创建的线程
- **提高响应速度**：任务到达时无需等待线程创建
- **提高线程可管理性**：统一分配、调优和监控

### 线程池核心参数
- **corePoolSize**：核心线程数
- **maximumPoolSize**：最大线程数
- **keepAliveTime**：线程存活时间
- **workQueue**：工作队列
- **threadFactory**：线程工厂
- **handler**：拒绝策略

### 常用线程池
```java
// 固定大小线程池
ExecutorService fixedPool = Executors.newFixedThreadPool(5);

// 缓存线程池
ExecutorService cachedPool = Executors.newCachedThreadPool();

// 单线程池
ExecutorService singlePool = Executors.newSingleThreadExecutor();

// 定时任务线程池
ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(3);
```

## 线程异常处理

### 异常捕获
```java
Thread thread = new Thread(() -> {
    try {
        // 可能抛出异常的代码
    } catch (Exception e) {
        // 异常处理
    }
});

// 设置未捕获异常处理器
thread.setUncaughtExceptionHandler((t, e) -> {
    System.out.println("线程异常: " + e.getMessage());
});
```

## 性能优化建议

1. **合理使用线程池**：避免频繁创建和销毁线程
2. **减少锁的持有时间**：提高并发性能
3. **使用无锁编程**：CAS操作、原子类
4. **避免死锁**：按顺序获取锁
5. **监控线程状态**：及时发现和解决问题

## 总结

Java线程是并发编程的基础，理解线程的创建、管理和同步机制对于编写高质量的Java程序至关重要。在实际开发中，应该优先使用线程池，合理设计同步机制，避免常见的线程安全问题。

—— 完 ——
