---
layout: post
title: Java线程池原理详解
subtitle: 线程池的组成、工作原理、拒绝策略与性能优化
date: 2018-10-05
categories: 技术
tags: java 线程池 并发 性能 多线程
cover: 
---

线程池是Java并发编程中的重要组件，理解线程池的原理、组成和工作机制对于编写高性能的并发程序至关重要。

## 线程池概述

### 线程池的作用
- **线程复用**：避免频繁创建和销毁线程的开销
- **资源管理**：统一管理线程资源
- **任务调度**：合理分配任务给线程执行
- **性能优化**：提高系统整体性能

### 线程池的优势
- **降低资源消耗**：复用已创建的线程
- **提高响应速度**：任务到达时无需等待线程创建
- **提高线程可管理性**：统一分配、调优和监控
- **提供更多功能**：定时执行、定期执行、线程中断等

## 线程池的组成

### 核心组件
```java
public class ThreadPoolComponents {
    // 1. 核心线程数 (corePoolSize)
    private int corePoolSize;
    
    // 2. 最大线程数 (maximumPoolSize)
    private int maximumPoolSize;
    
    // 3. 线程存活时间 (keepAliveTime)
    private long keepAliveTime;
    
    // 4. 时间单位 (unit)
    private TimeUnit unit;
    
    // 5. 工作队列 (workQueue)
    private BlockingQueue<Runnable> workQueue;
    
    // 6. 线程工厂 (threadFactory)
    private ThreadFactory threadFactory;
    
    // 7. 拒绝策略 (handler)
    private RejectedExecutionHandler handler;
}
```

### ThreadPoolExecutor构造方法
```java
public class ThreadPoolExecutorExample {
    public void createThreadPool() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            5,                              // 核心线程数
            10,                             // 最大线程数
            60L, TimeUnit.SECONDS,          // 线程存活时间
            new LinkedBlockingQueue<>(100), // 工作队列
            new ThreadFactory() {           // 线程工厂
                @Override
                public Thread newThread(Runnable r) {
                    Thread t = new Thread(r);
                    t.setName("CustomThread-" + t.getId());
                    return t;
                }
            },
            new ThreadPoolExecutor.CallerRunsPolicy() // 拒绝策略
        );
    }
}
```

## 线程池工作原理

### 工作流程
```java
public class ThreadPoolWorkflow {
    public void demonstrateWorkflow() {
        // 1. 任务提交
        // 2. 判断核心线程数
        // 3. 判断工作队列
        // 4. 判断最大线程数
        // 5. 执行拒绝策略
        
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(2)
        );
        
        // 提交任务
        for (int i = 0; i < 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("执行任务: " + taskId);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        executor.shutdown();
    }
}
```

### 线程池状态
```java
public class ThreadPoolStates {
    public void demonstrateStates() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(2)
        );
        
        // RUNNING: 接受新任务并处理队列中的任务
        System.out.println("线程池状态: " + executor.getState());
        
        // SHUTDOWN: 不接受新任务，但处理队列中的任务
        executor.shutdown();
        System.out.println("线程池状态: " + executor.getState());
        
        // STOP: 不接受新任务，不处理队列中的任务，中断正在执行的任务
        executor.shutdownNow();
        System.out.println("线程池状态: " + executor.getState());
        
        // TIDYING: 所有任务已终止，工作线程数为0
        // TERMINATED: 线程池已完全终止
    }
}
```

## 线程复用机制

### 线程复用原理
```java
public class ThreadReusePrinciple {
    public void demonstrateThreadReuse() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(2)
        );
        
        // 线程复用：同一个线程可以执行多个任务
        for (int i = 0; i < 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("线程: " + Thread.currentThread().getName() + 
                                 " 执行任务: " + taskId);
            });
        }
        
        executor.shutdown();
    }
}
```

### 线程生命周期管理
```java
public class ThreadLifecycleManagement {
    public void demonstrateLifecycle() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(2)
        ) {
            @Override
            protected void beforeExecute(Thread t, Runnable r) {
                System.out.println("线程执行前: " + t.getName());
            }
            
            @Override
            protected void afterExecute(Runnable r, Throwable t) {
                System.out.println("线程执行后: " + Thread.currentThread().getName());
            }
            
            @Override
            protected void terminated() {
                System.out.println("线程池终止");
            }
        };
        
        executor.submit(() -> System.out.println("任务执行"));
        executor.shutdown();
    }
}
```

## 工作队列

### 队列类型
```java
public class WorkQueueTypes {
    public void demonstrateQueueTypes() {
        // 1. 无界队列 - LinkedBlockingQueue
        ThreadPoolExecutor executor1 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>() // 无界队列
        );
        
        // 2. 有界队列 - ArrayBlockingQueue
        ThreadPoolExecutor executor2 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(10) // 有界队列
        );
        
        // 3. 同步队列 - SynchronousQueue
        ThreadPoolExecutor executor3 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new SynchronousQueue<>() // 同步队列
        );
        
        // 4. 优先级队列 - PriorityBlockingQueue
        ThreadPoolExecutor executor4 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new PriorityBlockingQueue<>() // 优先级队列
        );
    }
}
```

### 队列选择策略
```java
public class QueueSelectionStrategy {
    public void selectQueue() {
        // CPU密集型任务：使用有界队列
        ThreadPoolExecutor cpuIntensive = new ThreadPoolExecutor(
            Runtime.getRuntime().availableProcessors(),
            Runtime.getRuntime().availableProcessors(),
            60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(100)
        );
        
        // I/O密集型任务：使用无界队列
        ThreadPoolExecutor ioIntensive = new ThreadPoolExecutor(
            Runtime.getRuntime().availableProcessors(),
            Runtime.getRuntime().availableProcessors() * 2,
            60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>()
        );
        
        // 混合型任务：使用有界队列
        ThreadPoolExecutor mixed = new ThreadPoolExecutor(
            Runtime.getRuntime().availableProcessors(),
            Runtime.getRuntime().availableProcessors() * 2,
            60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(200)
        );
    }
}
```

## 拒绝策略

### 内置拒绝策略
```java
public class RejectionPolicies {
    public void demonstrateRejectionPolicies() {
        // 1. AbortPolicy - 抛出异常
        ThreadPoolExecutor executor1 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(2),
            new ThreadPoolExecutor.AbortPolicy()
        );
        
        // 2. CallerRunsPolicy - 调用者执行
        ThreadPoolExecutor executor2 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(2),
            new ThreadPoolExecutor.CallerRunsPolicy()
        );
        
        // 3. DiscardPolicy - 丢弃任务
        ThreadPoolExecutor executor3 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(2),
            new ThreadPoolExecutor.DiscardPolicy()
        );
        
        // 4. DiscardOldestPolicy - 丢弃最老的任务
        ThreadPoolExecutor executor4 = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(2),
            new ThreadPoolExecutor.DiscardOldestPolicy()
        );
    }
}
```

### 自定义拒绝策略
```java
public class CustomRejectionPolicy {
    public void createCustomPolicy() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(2),
            new RejectedExecutionHandler() {
                @Override
                public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
                    // 自定义拒绝策略
                    System.out.println("任务被拒绝: " + r);
                    
                    // 可以选择：
                    // 1. 记录日志
                    // 2. 保存到数据库
                    // 3. 发送到消息队列
                    // 4. 重试机制
                }
            }
        );
    }
}
```

## 线程池监控

### 监控指标
```java
public class ThreadPoolMonitoring {
    public void monitorThreadPool() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(10)
        );
        
        // 提交任务
        for (int i = 0; i < 10; i++) {
            executor.submit(() -> {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        // 监控指标
        System.out.println("核心线程数: " + executor.getCorePoolSize());
        System.out.println("最大线程数: " + executor.getMaximumPoolSize());
        System.out.println("当前线程数: " + executor.getPoolSize());
        System.out.println("活跃线程数: " + executor.getActiveCount());
        System.out.println("已完成任务数: " + executor.getCompletedTaskCount());
        System.out.println("总任务数: " + executor.getTaskCount());
        System.out.println("队列大小: " + executor.getQueue().size());
        
        executor.shutdown();
    }
}
```

### 性能监控
```java
public class PerformanceMonitoring {
    public void monitorPerformance() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(10)
        );
        
        long startTime = System.currentTimeMillis();
        
        // 提交任务
        for (int i = 0; i < 100; i++) {
            executor.submit(() -> {
                // 模拟任务执行
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        executor.shutdown();
        
        try {
            executor.awaitTermination(60, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        long endTime = System.currentTimeMillis();
        System.out.println("总耗时: " + (endTime - startTime) + "ms");
        System.out.println("平均每个任务耗时: " + (endTime - startTime) / 100.0 + "ms");
    }
}
```

## 线程池优化

### 参数调优
```java
public class ThreadPoolOptimization {
    public void optimizeThreadPool() {
        int cpuCount = Runtime.getRuntime().availableProcessors();
        
        // CPU密集型任务优化
        ThreadPoolExecutor cpuIntensive = new ThreadPoolExecutor(
            cpuCount,                    // 核心线程数 = CPU核心数
            cpuCount,                    // 最大线程数 = CPU核心数
            60L, TimeUnit.SECONDS,      // 线程存活时间
            new ArrayBlockingQueue<>(100), // 有界队列
            new ThreadPoolExecutor.CallerRunsPolicy() // 拒绝策略
        );
        
        // I/O密集型任务优化
        ThreadPoolExecutor ioIntensive = new ThreadPoolExecutor(
            cpuCount,                    // 核心线程数 = CPU核心数
            cpuCount * 2,                // 最大线程数 = CPU核心数 * 2
            60L, TimeUnit.SECONDS,      // 线程存活时间
            new LinkedBlockingQueue<>(), // 无界队列
            new ThreadPoolExecutor.CallerRunsPolicy() // 拒绝策略
        );
        
        // 混合型任务优化
        ThreadPoolExecutor mixed = new ThreadPoolExecutor(
            cpuCount,                    // 核心线程数 = CPU核心数
            cpuCount * 2,                // 最大线程数 = CPU核心数 * 2
            60L, TimeUnit.SECONDS,      // 线程存活时间
            new ArrayBlockingQueue<>(200), // 有界队列
            new ThreadPoolExecutor.CallerRunsPolicy() // 拒绝策略
        );
    }
}
```

### 动态调整
```java
public class DynamicAdjustment {
    public void adjustThreadPool() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(10)
        );
        
        // 动态调整核心线程数
        executor.setCorePoolSize(3);
        
        // 动态调整最大线程数
        executor.setMaximumPoolSize(6);
        
        // 动态调整线程存活时间
        executor.setKeepAliveTime(120L, TimeUnit.SECONDS);
        
        // 预热线程池
        executor.prestartAllCoreThreads();
    }
}
```

## 最佳实践

### 1. 线程池选择
```java
public class ThreadPoolSelection {
    public void selectThreadPool() {
        // 1. 固定大小线程池 - 适合CPU密集型任务
        ExecutorService fixedPool = Executors.newFixedThreadPool(
            Runtime.getRuntime().availableProcessors()
        );
        
        // 2. 缓存线程池 - 适合I/O密集型任务
        ExecutorService cachedPool = Executors.newCachedThreadPool();
        
        // 3. 单线程池 - 适合需要顺序执行的任务
        ExecutorService singlePool = Executors.newSingleThreadExecutor();
        
        // 4. 定时线程池 - 适合定时任务
        ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(2);
    }
}
```

### 2. 异常处理
```java
public class ExceptionHandling {
    public void handleExceptions() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(10)
        ) {
            @Override
            protected void afterExecute(Runnable r, Throwable t) {
                if (t != null) {
                    System.err.println("任务执行异常: " + t.getMessage());
                    t.printStackTrace();
                }
            }
        };
        
        // 提交任务时处理异常
        executor.submit(() -> {
            try {
                // 可能抛出异常的任务
                throw new RuntimeException("任务执行失败");
            } catch (Exception e) {
                System.err.println("捕获异常: " + e.getMessage());
            }
        });
    }
}
```

### 3. 资源管理
```java
public class ResourceManagement {
    public void manageResources() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 4, 60L, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(10)
        );
        
        try {
            // 使用线程池
            for (int i = 0; i < 10; i++) {
                executor.submit(() -> {
                    // 任务逻辑
                });
            }
        } finally {
            // 关闭线程池
            executor.shutdown();
            
            try {
                // 等待任务完成
                if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                    // 强制关闭
                    executor.shutdownNow();
                }
            } catch (InterruptedException e) {
                executor.shutdownNow();
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

## 总结

线程池是Java并发编程中的核心组件，理解其原理、组成和工作机制对于编写高性能的并发程序至关重要。通过合理配置线程池参数、选择合适的拒绝策略、进行性能监控和优化，可以充分发挥线程池的优势，提高系统的整体性能。

—— 完 ——
