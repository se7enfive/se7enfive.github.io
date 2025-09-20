---
layout: post
title: Java同步工具类详解
subtitle: CyclicBarrier、CountDownLatch、Semaphore的使用场景与实现原理
date: 2018-10-19
categories: 技术
tags: java 同步工具 并发 多线程 协调
cover: 
---

Java提供了多种同步工具类来协调多线程之间的执行，理解CyclicBarrier、CountDownLatch、Semaphore的使用场景和实现原理对于编写复杂的并发程序至关重要。

## 同步工具类概述

### 同步工具类的作用
- **线程协调**：协调多个线程的执行顺序
- **同步控制**：控制线程的等待和唤醒
- **资源管理**：管理共享资源的访问
- **并发模式**：实现各种并发设计模式

### 主要同步工具类
- **CountDownLatch**：倒计时门闩
- **CyclicBarrier**：循环栅栏
- **Semaphore**：信号量
- **Exchanger**：交换器
- **Phaser**：阶段器

## CountDownLatch - 倒计时门闩

### CountDownLatch特点
- **一次性使用**：计数器只能递减，不能重置
- **等待机制**：一个或多个线程等待其他线程完成
- **不可重复**：计数器到达0后不能重新使用

### CountDownLatch基本用法
```java
public class CountDownLatchExample {
    public void demonstrateCountDownLatch() {
        int workerCount = 3;
        CountDownLatch latch = new CountDownLatch(workerCount);
        
        // 工作线程
        for (int i = 0; i < workerCount; i++) {
            final int workerId = i;
            Thread worker = new Thread(() -> {
                try {
                    System.out.println("工作线程 " + workerId + " 开始工作");
                    Thread.sleep(1000 + workerId * 500); // 模拟工作时间
                    System.out.println("工作线程 " + workerId + " 完成工作");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    latch.countDown(); // 计数器减1
                }
            });
            worker.start();
        }
        
        // 主线程等待所有工作线程完成
        try {
            System.out.println("主线程等待所有工作线程完成...");
            latch.await(); // 等待计数器到达0
            System.out.println("所有工作线程已完成，主线程继续执行");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### CountDownLatch高级用法
```java
public class CountDownLatchAdvanced {
    public void demonstrateAdvancedUsage() {
        int taskCount = 5;
        CountDownLatch startSignal = new CountDownLatch(1);
        CountDownLatch doneSignal = new CountDownLatch(taskCount);
        
        // 启动工作线程
        for (int i = 0; i < taskCount; i++) {
            final int taskId = i;
            Thread worker = new Thread(() -> {
                try {
                    // 等待开始信号
                    startSignal.await();
                    System.out.println("任务 " + taskId + " 开始执行");
                    Thread.sleep(1000 + taskId * 200);
                    System.out.println("任务 " + taskId + " 执行完成");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    doneSignal.countDown();
                }
            });
            worker.start();
        }
        
        // 主线程控制
        try {
            System.out.println("准备开始所有任务...");
            Thread.sleep(1000); // 准备时间
            startSignal.countDown(); // 发出开始信号
            System.out.println("所有任务开始执行");
            
            doneSignal.await(); // 等待所有任务完成
            System.out.println("所有任务执行完成");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### CountDownLatch实现原理
```java
public class CountDownLatchPrinciple {
    public void demonstratePrinciple() {
        // CountDownLatch内部使用AQS实现
        // 1. 初始化时设置state为计数器的值
        // 2. await()方法会阻塞当前线程，直到state为0
        // 3. countDown()方法会将state减1
        // 4. 当state为0时，唤醒所有等待的线程
        
        CountDownLatch latch = new CountDownLatch(3);
        
        // 模拟CountDownLatch的工作过程
        System.out.println("初始状态: " + latch.getCount());
        
        latch.countDown();
        System.out.println("第一次countDown: " + latch.getCount());
        
        latch.countDown();
        System.out.println("第二次countDown: " + latch.getCount());
        
        latch.countDown();
        System.out.println("第三次countDown: " + latch.getCount());
    }
}
```

## CyclicBarrier - 循环栅栏

### CyclicBarrier特点
- **可重复使用**：可以多次使用，计数器会自动重置
- **等待机制**：所有线程都到达栅栏后才能继续执行
- **回调支持**：支持到达栅栏时的回调操作

### CyclicBarrier基本用法
```java
public class CyclicBarrierExample {
    public void demonstrateCyclicBarrier() {
        int threadCount = 3;
        CyclicBarrier barrier = new CyclicBarrier(threadCount, () -> {
            System.out.println("所有线程都到达栅栏，开始下一阶段");
        });
        
        // 启动多个线程
        for (int i = 0; i < threadCount; i++) {
            final int threadId = i;
            Thread thread = new Thread(() -> {
                try {
                    System.out.println("线程 " + threadId + " 开始工作");
                    Thread.sleep(1000 + threadId * 500);
                    System.out.println("线程 " + threadId + " 到达栅栏");
                    barrier.await(); // 等待其他线程到达栅栏
                    System.out.println("线程 " + threadId + " 继续执行");
                } catch (InterruptedException | BrokenBarrierException e) {
                    Thread.currentThread().interrupt();
                }
            });
            thread.start();
        }
    }
}
```

### CyclicBarrier高级用法
```java
public class CyclicBarrierAdvanced {
    public void demonstrateAdvancedUsage() {
        int threadCount = 3;
        CyclicBarrier barrier = new CyclicBarrier(threadCount);
        
        // 多轮执行
        for (int round = 0; round < 3; round++) {
            System.out.println("第 " + (round + 1) + " 轮开始");
            
            // 启动线程
            for (int i = 0; i < threadCount; i++) {
                final int threadId = i;
                final int currentRound = round;
                Thread thread = new Thread(() -> {
                    try {
                        System.out.println("线程 " + threadId + " 第 " + (currentRound + 1) + " 轮开始");
                        Thread.sleep(1000 + threadId * 200);
                        System.out.println("线程 " + threadId + " 第 " + (currentRound + 1) + " 轮完成");
                        barrier.await(); // 等待其他线程
                        System.out.println("线程 " + threadId + " 第 " + (currentRound + 1) + " 轮继续");
                    } catch (InterruptedException | BrokenBarrierException e) {
                        Thread.currentThread().interrupt();
                    }
                });
                thread.start();
            }
            
            // 等待当前轮完成
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

### CyclicBarrier与CountDownLatch对比
```java
public class BarrierVsLatch {
    public void compareBarrierAndLatch() {
        // CountDownLatch: 一个线程等待多个线程完成
        CountDownLatch latch = new CountDownLatch(3);
        
        // CyclicBarrier: 多个线程相互等待
        CyclicBarrier barrier = new CyclicBarrier(3);
        
        // 使用场景对比
        System.out.println("CountDownLatch: 主线程等待工作线程完成");
        System.out.println("CyclicBarrier: 多个线程同步执行");
    }
}
```

## Semaphore - 信号量

### Semaphore特点
- **资源控制**：控制同时访问资源的线程数量
- **许可证机制**：通过许可证控制访问
- **公平性**：支持公平和非公平模式

### Semaphore基本用法
```java
public class SemaphoreExample {
    public void demonstrateSemaphore() {
        int permitCount = 2;
        Semaphore semaphore = new Semaphore(permitCount);
        
        // 启动多个线程
        for (int i = 0; i < 5; i++) {
            final int threadId = i;
            Thread thread = new Thread(() -> {
                try {
                    System.out.println("线程 " + threadId + " 尝试获取许可证");
                    semaphore.acquire(); // 获取许可证
                    System.out.println("线程 " + threadId + " 获得许可证，开始工作");
                    Thread.sleep(2000); // 模拟工作
                    System.out.println("线程 " + threadId + " 工作完成，释放许可证");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    semaphore.release(); // 释放许可证
                }
            });
            thread.start();
        }
    }
}
```

### Semaphore高级用法
```java
public class SemaphoreAdvanced {
    public void demonstrateAdvancedUsage() {
        // 公平信号量
        Semaphore fairSemaphore = new Semaphore(2, true);
        
        // 非公平信号量
        Semaphore unfairSemaphore = new Semaphore(2, false);
        
        // 尝试获取许可证
        Semaphore semaphore = new Semaphore(2);
        
        for (int i = 0; i < 5; i++) {
            final int threadId = i;
            Thread thread = new Thread(() -> {
                try {
                    // 尝试获取许可证，最多等待1秒
                    if (semaphore.tryAcquire(1, TimeUnit.SECONDS)) {
                        System.out.println("线程 " + threadId + " 获得许可证");
                        Thread.sleep(2000);
                        System.out.println("线程 " + threadId + " 释放许可证");
                        semaphore.release();
                    } else {
                        System.out.println("线程 " + threadId + " 获取许可证超时");
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
            thread.start();
        }
    }
}
```

### Semaphore实现原理
```java
public class SemaphorePrinciple {
    public void demonstratePrinciple() {
        // Semaphore内部使用AQS实现
        // 1. 初始化时设置state为许可证数量
        // 2. acquire()方法会尝试获取许可证，如果失败则阻塞
        // 3. release()方法会释放许可证，唤醒等待的线程
        // 4. 支持公平和非公平模式
        
        Semaphore semaphore = new Semaphore(3);
        
        // 获取可用许可证数量
        System.out.println("可用许可证: " + semaphore.availablePermits());
        
        // 获取许可证
        try {
            semaphore.acquire();
            System.out.println("获取许可证后，可用许可证: " + semaphore.availablePermits());
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        // 释放许可证
        semaphore.release();
        System.out.println("释放许可证后，可用许可证: " + semaphore.availablePermits());
    }
}
```

## 实际应用场景

### 1. 数据库连接池
```java
public class DatabaseConnectionPool {
    private final Semaphore semaphore;
    private final Queue<Connection> connections;
    
    public DatabaseConnectionPool(int maxConnections) {
        this.semaphore = new Semaphore(maxConnections);
        this.connections = new ConcurrentLinkedQueue<>();
        
        // 初始化连接池
        for (int i = 0; i < maxConnections; i++) {
            connections.offer(createConnection());
        }
    }
    
    public Connection getConnection() throws InterruptedException {
        semaphore.acquire(); // 获取许可证
        return connections.poll();
    }
    
    public void releaseConnection(Connection connection) {
        connections.offer(connection);
        semaphore.release(); // 释放许可证
    }
    
    private Connection createConnection() {
        // 创建数据库连接
        return null;
    }
}
```

### 2. 多阶段任务协调
```java
public class MultiPhaseTask {
    public void demonstrateMultiPhaseTask() {
        int threadCount = 3;
        CyclicBarrier barrier = new CyclicBarrier(threadCount);
        
        // 多阶段任务
        for (int phase = 0; phase < 3; phase++) {
            System.out.println("第 " + (phase + 1) + " 阶段开始");
            
            for (int i = 0; i < threadCount; i++) {
                final int threadId = i;
                final int currentPhase = phase;
                Thread thread = new Thread(() -> {
                    try {
                        System.out.println("线程 " + threadId + " 第 " + (currentPhase + 1) + " 阶段开始");
                        Thread.sleep(1000 + threadId * 200);
                        System.out.println("线程 " + threadId + " 第 " + (currentPhase + 1) + " 阶段完成");
                        barrier.await(); // 等待其他线程
                    } catch (InterruptedException | BrokenBarrierException e) {
                        Thread.currentThread().interrupt();
                    }
                });
                thread.start();
            }
            
            // 等待当前阶段完成
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

### 3. 限流器
```java
public class RateLimiter {
    private final Semaphore semaphore;
    private final ScheduledExecutorService scheduler;
    
    public RateLimiter(int permits, long period, TimeUnit unit) {
        this.semaphore = new Semaphore(permits);
        this.scheduler = Executors.newScheduledThreadPool(1);
        
        // 定期释放许可证
        scheduler.scheduleAtFixedRate(() -> {
            semaphore.release();
        }, period, period, unit);
    }
    
    public boolean tryAcquire() {
        return semaphore.tryAcquire();
    }
    
    public void acquire() throws InterruptedException {
        semaphore.acquire();
    }
    
    public void shutdown() {
        scheduler.shutdown();
    }
}
```

## 最佳实践

### 1. 异常处理
```java
public class ExceptionHandling {
    public void handleExceptions() {
        CountDownLatch latch = new CountDownLatch(3);
        
        try {
            latch.await();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        CyclicBarrier barrier = new CyclicBarrier(3);
        
        try {
            barrier.await();
        } catch (InterruptedException | BrokenBarrierException e) {
            Thread.currentThread().interrupt();
        }
        
        Semaphore semaphore = new Semaphore(2);
        
        try {
            semaphore.acquire();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            semaphore.release();
        }
    }
}
```

### 2. 资源管理
```java
public class ResourceManagement {
    public void manageResources() {
        Semaphore semaphore = new Semaphore(2);
        
        try {
            semaphore.acquire();
            // 使用资源
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            semaphore.release();
        }
    }
}
```

### 3. 性能优化
```java
public class PerformanceOptimization {
    public void optimizePerformance() {
        // 选择合适的同步工具
        // 1. 一个线程等待多个线程完成 -> CountDownLatch
        // 2. 多个线程相互等待 -> CyclicBarrier
        // 3. 控制资源访问 -> Semaphore
        
        // 避免不必要的阻塞
        Semaphore semaphore = new Semaphore(2);
        
        if (semaphore.tryAcquire()) {
            try {
                // 处理逻辑
            } finally {
                semaphore.release();
            }
        } else {
            // 处理无法获取资源的情况
        }
    }
}
```

## 总结

CyclicBarrier、CountDownLatch、Semaphore是Java并发编程中的重要同步工具类，它们各有特点和使用场景。通过理解它们的原理和正确使用，可以编写出更加高效和可靠的并发程序。

—— 完 ——
