---
layout: post
title: Java同步锁与死锁详解
subtitle: 同步机制、死锁产生原因、检测与预防策略
date: 2018-09-28
categories: 技术
tags: java 同步 死锁 并发 多线程
cover: 
---

同步锁是Java并发编程中的核心机制，而死锁是并发编程中最严重的问题之一。理解同步锁的原理和死锁的预防策略对于编写安全的并发程序至关重要。

## 同步锁概述

### 同步锁的作用
- **互斥访问**：确保同一时间只有一个线程访问共享资源
- **可见性**：保证线程对共享变量的修改对其他线程可见
- **有序性**：保证程序执行的顺序性

### 同步锁的类型
- **synchronized关键字**：Java内置同步机制
- **ReentrantLock**：可重入锁
- **ReadWriteLock**：读写锁
- **StampedLock**：邮戳锁

## synchronized关键字

### synchronized基本用法
```java
public class SynchronizedExample {
    private int count = 0;
    private final Object lock = new Object();
    
    // 1. 同步方法
    public synchronized void increment() {
        count++;
    }
    
    // 2. 同步代码块
    public void incrementWithBlock() {
        synchronized (this) {
            count++;
        }
    }
    
    // 3. 同步静态方法
    public static synchronized void staticMethod() {
        // 静态方法同步
    }
    
    // 4. 同步代码块（指定锁对象）
    public void incrementWithObject() {
        synchronized (lock) {
            count++;
        }
    }
}
```

### synchronized实现原理
```java
public class SynchronizedPrinciple {
    public void demonstrateSynchronized() {
        synchronized (this) {
            // 1. 获取锁
            // 2. 执行同步代码
            // 3. 释放锁
        }
    }
    
    // 字节码层面
    public void synchronizedMethod() {
        // monitorenter - 获取锁
        synchronized (this) {
            // 同步代码块
        }
        // monitorexit - 释放锁
    }
}
```

### synchronized的锁升级
```java
public class LockUpgrade {
    private int count = 0;
    
    public void demonstrateLockUpgrade() {
        // 1. 无锁状态
        // 2. 偏向锁：只有一个线程访问
        // 3. 轻量级锁：多个线程竞争但不激烈
        // 4. 重量级锁：多个线程激烈竞争
        
        synchronized (this) {
            count++;
        }
    }
}
```

## ReentrantLock可重入锁

### ReentrantLock基本用法
```java
public class ReentrantLockExample {
    private final ReentrantLock lock = new ReentrantLock();
    private int count = 0;
    
    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
    
    public void tryLockExample() {
        if (lock.tryLock()) {
            try {
                // 获取锁成功
                count++;
            } finally {
                lock.unlock();
            }
        } else {
            // 获取锁失败
            System.out.println("无法获取锁");
        }
    }
    
    public void tryLockWithTimeout() {
        try {
            if (lock.tryLock(1, TimeUnit.SECONDS)) {
                try {
                    count++;
                } finally {
                    lock.unlock();
                }
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### ReentrantLock高级特性
```java
public class ReentrantLockAdvanced {
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean conditionMet = false;
    
    public void waitForCondition() throws InterruptedException {
        lock.lock();
        try {
            while (!conditionMet) {
                condition.await();
            }
            // 条件满足，继续执行
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
    
    public void demonstrateInterruptibleLock() {
        try {
            lock.lockInterruptibly();
            try {
                // 可中断的锁获取
            } finally {
                lock.unlock();
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

## 死锁的产生

### 死锁的四个必要条件
1. **互斥条件**：资源不能被多个线程同时使用
2. **请求和保持条件**：线程持有资源的同时请求其他资源
3. **不可剥夺条件**：资源不能被强制释放
4. **循环等待条件**：存在线程资源的循环等待链

### 死锁示例
```java
public class DeadlockExample {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) {
            System.out.println("线程1获取lock1");
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            
            synchronized (lock2) {
                System.out.println("线程1获取lock2");
            }
        }
    }
    
    public void method2() {
        synchronized (lock2) {
            System.out.println("线程2获取lock2");
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            
            synchronized (lock1) {
                System.out.println("线程2获取lock1");
            }
        }
    }
    
    public void demonstrateDeadlock() {
        Thread thread1 = new Thread(this::method1);
        Thread thread2 = new Thread(this::method2);
        
        thread1.start();
        thread2.start();
    }
}
```

### 死锁检测
```java
public class DeadlockDetection {
    public void detectDeadlock() {
        ThreadMXBean threadBean = ManagementFactory.getThreadMXBean();
        long[] deadlockedThreads = threadBean.findDeadlockedThreads();
        
        if (deadlockedThreads != null) {
            ThreadInfo[] threadInfos = threadBean.getThreadInfo(deadlockedThreads);
            
            for (ThreadInfo info : threadInfos) {
                System.out.println("死锁线程: " + info.getThreadName());
                System.out.println("线程状态: " + info.getThreadState());
                
                LockInfo[] lockInfos = info.getLockedSynchronizers();
                for (LockInfo lockInfo : lockInfos) {
                    System.out.println("锁信息: " + lockInfo);
                }
            }
        }
    }
}
```

## 死锁预防策略

### 1. 避免嵌套锁
```java
public class AvoidNestedLocks {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    // 错误示例：嵌套锁
    public void badMethod() {
        synchronized (lock1) {
            synchronized (lock2) {
                // 可能导致死锁
            }
        }
    }
    
    // 正确示例：避免嵌套锁
    public void goodMethod() {
        synchronized (lock1) {
            // 处理lock1相关的逻辑
        }
        
        synchronized (lock2) {
            // 处理lock2相关的逻辑
        }
    }
}
```

### 2. 锁顺序化
```java
public class LockOrdering {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) {
            synchronized (lock2) {
                // 处理逻辑
            }
        }
    }
    
    public void method2() {
        synchronized (lock1) { // 保持相同的锁顺序
            synchronized (lock2) {
                // 处理逻辑
            }
        }
    }
}
```

### 3. 使用超时锁
```java
public class TimeoutLock {
    private final ReentrantLock lock1 = new ReentrantLock();
    private final ReentrantLock lock2 = new ReentrantLock();
    
    public void method1() {
        if (lock1.tryLock(1, TimeUnit.SECONDS)) {
            try {
                if (lock2.tryLock(1, TimeUnit.SECONDS)) {
                    try {
                        // 处理逻辑
                    } finally {
                        lock2.unlock();
                    }
                }
            } finally {
                lock1.unlock();
            }
        }
    }
    
    public void method2() {
        if (lock2.tryLock(1, TimeUnit.SECONDS)) {
            try {
                if (lock1.tryLock(1, TimeUnit.SECONDS)) {
                    try {
                        // 处理逻辑
                    } finally {
                        lock1.unlock();
                    }
                }
            } finally {
                lock2.unlock();
            }
        }
    }
}
```

### 4. 使用锁层次结构
```java
public class LockHierarchy {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) {
            synchronized (lock2) {
                // 处理逻辑
            }
        }
    }
    
    public void method2() {
        synchronized (lock1) {
            synchronized (lock2) {
                // 处理逻辑
            }
        }
    }
}
```

## 死锁恢复策略

### 1. 线程中断
```java
public class DeadlockRecovery {
    private final ReentrantLock lock1 = new ReentrantLock();
    private final ReentrantLock lock2 = new ReentrantLock();
    
    public void method1() {
        try {
            if (lock1.tryLock(1, TimeUnit.SECONDS)) {
                try {
                    if (lock2.tryLock(1, TimeUnit.SECONDS)) {
                        try {
                            // 处理逻辑
                        } finally {
                            lock2.unlock();
                        }
                    }
                } finally {
                    lock1.unlock();
                }
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    public void method2() {
        try {
            if (lock2.tryLock(1, TimeUnit.SECONDS)) {
                try {
                    if (lock1.tryLock(1, TimeUnit.SECONDS)) {
                        try {
                            // 处理逻辑
                        } finally {
                            lock1.unlock();
                        }
                    }
                } finally {
                    lock2.unlock();
                }
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### 2. 锁超时
```java
public class LockTimeout {
    private final ReentrantLock lock1 = new ReentrantLock();
    private final ReentrantLock lock2 = new ReentrantLock();
    
    public void method1() {
        try {
            if (lock1.tryLock(1, TimeUnit.SECONDS)) {
                try {
                    if (lock2.tryLock(1, TimeUnit.SECONDS)) {
                        try {
                            // 处理逻辑
                        } finally {
                            lock2.unlock();
                        }
                    } else {
                        // 获取lock2超时，释放lock1
                        System.out.println("获取lock2超时");
                    }
                } finally {
                    lock1.unlock();
                }
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

## 同步性能优化

### 1. 减少锁的粒度
```java
public class LockGranularity {
    private final Object lock = new Object();
    private int count1 = 0;
    private int count2 = 0;
    
    // 错误示例：粗粒度锁
    public void badMethod() {
        synchronized (lock) {
            count1++;
            count2++;
        }
    }
    
    // 正确示例：细粒度锁
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void goodMethod() {
        synchronized (lock1) {
            count1++;
        }
        
        synchronized (lock2) {
            count2++;
        }
    }
}
```

### 2. 使用读写锁
```java
public class ReadWriteLockExample {
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    private final Lock readLock = lock.readLock();
    private final Lock writeLock = lock.writeLock();
    private int value = 0;
    
    public int readValue() {
        readLock.lock();
        try {
            return value;
        } finally {
            readLock.unlock();
        }
    }
    
    public void writeValue(int newValue) {
        writeLock.lock();
        try {
            value = newValue;
        } finally {
            writeLock.unlock();
        }
    }
}
```

### 3. 使用无锁数据结构
```java
public class LockFreeDataStructures {
    private final AtomicInteger counter = new AtomicInteger(0);
    private final ConcurrentHashMap<String, String> map = new ConcurrentHashMap<>();
    
    public void increment() {
        counter.incrementAndGet();
    }
    
    public void putValue(String key, String value) {
        map.put(key, value);
    }
    
    public String getValue(String key) {
        return map.get(key);
    }
}
```

## 最佳实践

### 1. 锁的使用原则
```java
public class LockBestPractices {
    // 1. 总是使用try-finally释放锁
    public void useTryFinally() {
        ReentrantLock lock = new ReentrantLock();
        lock.lock();
        try {
            // 处理逻辑
        } finally {
            lock.unlock();
        }
    }
    
    // 2. 避免在锁内调用外部方法
    public void avoidExternalCallsInLock() {
        synchronized (this) {
            // 避免调用外部方法
            // externalMethod(); // 危险
        }
    }
    
    // 3. 使用锁顺序化
    public void useLockOrdering() {
        // 总是按相同顺序获取锁
        synchronized (lock1) {
            synchronized (lock2) {
                // 处理逻辑
            }
        }
    }
}
```

### 2. 死锁预防检查清单
```java
public class DeadlockPreventionChecklist {
    // 1. 检查锁顺序
    public void checkLockOrder() {
        // 确保所有线程按相同顺序获取锁
    }
    
    // 2. 检查锁超时
    public void checkLockTimeout() {
        // 使用超时锁避免无限等待
    }
    
    // 3. 检查锁粒度
    public void checkLockGranularity() {
        // 使用最小必要的锁粒度
    }
    
    // 4. 检查锁释放
    public void checkLockRelease() {
        // 确保锁在finally块中释放
    }
}
```

## 总结

同步锁和死锁是Java并发编程中的核心概念。通过理解同步机制的原理、死锁的产生原因和预防策略，可以编写出安全、高效的并发程序。在实际开发中，应该遵循锁的使用原则，采用合适的死锁预防策略，确保程序的正确性和性能。

—— 完 ——
