---
layout: post
title: Java线程基本方法详解
subtitle: wait、sleep、yield、interrupt等线程控制方法的使用与原理
date: 2018-09-14
categories: 技术
tags: java 线程 并发 多线程
cover: 
---

Java线程编程中，掌握线程的基本控制方法是并发编程的基础。理解wait、sleep、yield、interrupt等方法的使用场景和原理对于编写高质量的并发程序至关重要。

## 线程基本方法概述

### 线程控制方法分类
- **等待方法**：wait() - 线程等待
- **睡眠方法**：sleep() - 线程睡眠
- **让步方法**：yield() - 线程让步
- **中断方法**：interrupt() - 线程中断
- **其他方法**：join()、notify()、notifyAll()等

### 方法特点对比
| 方法 | 作用 | 释放锁 | 可中断 | 使用场景 |
|------|------|--------|--------|----------|
| wait() | 等待 | 是 | 是 | 线程同步 |
| sleep() | 睡眠 | 否 | 是 | 延时操作 |
| yield() | 让步 | 否 | 否 | 线程调度 |
| interrupt() | 中断 | 否 | - | 线程控制 |

## wait() - 线程等待

### wait()方法特点
- **释放锁**：调用wait()会释放当前对象的锁
- **可中断**：可以被interrupt()中断
- **必须在同步块中调用**：必须在synchronized方法或块中调用
- **需要被唤醒**：需要其他线程调用notify()或notifyAll()唤醒

### wait()方法定义
```java
public final void wait() throws InterruptedException
public final void wait(long timeout) throws InterruptedException
public final void wait(long timeout, int nanos) throws InterruptedException
```

### wait()使用示例
```java
public class WaitExample {
    private final Object lock = new Object();
    private boolean condition = false;
    
    public void waitForCondition() throws InterruptedException {
        synchronized (lock) {
            while (!condition) {
                System.out.println("线程等待条件满足...");
                lock.wait(); // 释放锁，等待被唤醒
            }
            System.out.println("条件满足，继续执行");
        }
    }
    
    public void setCondition() {
        synchronized (lock) {
            condition = true;
            lock.notify(); // 唤醒等待的线程
        }
    }
}
```

### wait()方法原理
```java
public class WaitPrinciple {
    public void demonstrateWait() {
        Object obj = new Object();
        
        synchronized (obj) {
            try {
                // 1. 检查当前线程是否持有锁
                // 2. 释放锁
                // 3. 将线程加入等待队列
                // 4. 线程进入WAITING状态
                obj.wait();
            } catch (InterruptedException e) {
                // 处理中断异常
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

## sleep() - 线程睡眠

### sleep()方法特点
- **不释放锁**：调用sleep()不会释放当前对象的锁
- **可中断**：可以被interrupt()中断
- **精确延时**：提供精确的延时功能
- **线程状态**：线程进入TIMED_WAITING状态

### sleep()方法定义
```java
public static native void sleep(long millis) throws InterruptedException
public static void sleep(long millis, int nanos) throws InterruptedException
```

### sleep()使用示例
```java
public class SleepExample {
    public void demonstrateSleep() {
        try {
            System.out.println("开始睡眠...");
            Thread.sleep(1000); // 睡眠1秒
            System.out.println("睡眠结束");
        } catch (InterruptedException e) {
            System.out.println("睡眠被中断");
            Thread.currentThread().interrupt();
        }
    }
    
    public void sleepWithLock() {
        synchronized (this) {
            try {
                System.out.println("持有锁，开始睡眠...");
                Thread.sleep(2000); // 睡眠2秒，不释放锁
                System.out.println("睡眠结束，释放锁");
            } catch (InterruptedException e) {
                System.out.println("睡眠被中断");
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

### sleep()方法原理
```java
public class SleepPrinciple {
    public void demonstrateSleep() {
        try {
            // 1. 检查参数有效性
            // 2. 设置线程状态为TIMED_WAITING
            // 3. 调用系统sleep方法
            // 4. 等待指定时间后恢复
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            // 处理中断异常
            Thread.currentThread().interrupt();
        }
    }
}
```

## yield() - 线程让步

### yield()方法特点
- **不释放锁**：调用yield()不会释放当前对象的锁
- **不可中断**：不能被interrupt()中断
- **建议性**：只是建议让出CPU，不保证一定让出
- **线程状态**：线程保持RUNNABLE状态

### yield()方法定义
```java
public static native void yield()
```

### yield()使用示例
```java
public class YieldExample {
    public void demonstrateYield() {
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("线程1: " + i);
                Thread.yield(); // 建议让出CPU
            }
        });
        
        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("线程2: " + i);
                Thread.yield(); // 建议让出CPU
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

### yield()方法原理
```java
public class YieldPrinciple {
    public void demonstrateYield() {
        // 1. 检查当前线程状态
        // 2. 建议调度器让出CPU
        // 3. 线程保持RUNNABLE状态
        // 4. 调度器决定是否让出CPU
        Thread.yield();
    }
}
```

## interrupt() - 线程中断

### interrupt()方法特点
- **中断标志**：设置线程的中断标志
- **可中断方法**：可以中断wait()、sleep()、join()等方法
- **非强制**：不会强制终止线程
- **状态检查**：需要线程主动检查中断状态

### interrupt()方法定义
```java
public void interrupt()
public static boolean interrupted()
public boolean isInterrupted()
```

### interrupt()使用示例
```java
public class InterruptExample {
    public void demonstrateInterrupt() {
        Thread thread = new Thread(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try {
                    System.out.println("线程运行中...");
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    System.out.println("线程被中断");
                    Thread.currentThread().interrupt(); // 重新设置中断标志
                    break;
                }
            }
        });
        
        thread.start();
        
        try {
            Thread.sleep(3000);
            thread.interrupt(); // 中断线程
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### interrupt()方法原理
```java
public class InterruptPrinciple {
    public void demonstrateInterrupt() {
        Thread thread = new Thread(() -> {
            // 1. 检查中断标志
            while (!Thread.currentThread().isInterrupted()) {
                try {
                    // 2. 执行可中断操作
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    // 3. 处理中断异常
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        });
        
        thread.start();
        thread.interrupt(); // 设置中断标志
    }
}
```

## join() - 线程等待

### join()方法特点
- **等待完成**：等待目标线程完成
- **可中断**：可以被interrupt()中断
- **阻塞调用**：调用线程会阻塞直到目标线程完成
- **线程状态**：调用线程进入WAITING或TIMED_WAITING状态

### join()方法定义
```java
public final void join() throws InterruptedException
public final synchronized void join(long millis) throws InterruptedException
public final synchronized void join(long millis, int nanos) throws InterruptedException
```

### join()使用示例
```java
public class JoinExample {
    public void demonstrateJoin() {
        Thread thread1 = new Thread(() -> {
            try {
                Thread.sleep(2000);
                System.out.println("线程1完成");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        Thread thread2 = new Thread(() -> {
            try {
                Thread.sleep(1000);
                System.out.println("线程2完成");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        thread1.start();
        thread2.start();
        
        try {
            thread1.join(); // 等待线程1完成
            thread2.join(); // 等待线程2完成
            System.out.println("所有线程完成");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

## notify()和notifyAll() - 线程唤醒

### notify()方法特点
- **唤醒一个**：唤醒等待队列中的一个线程
- **随机选择**：随机选择要唤醒的线程
- **必须在同步块中调用**：必须在synchronized方法或块中调用
- **配合wait()使用**：通常与wait()方法配合使用

### notifyAll()方法特点
- **唤醒所有**：唤醒等待队列中的所有线程
- **竞争执行**：所有被唤醒的线程会竞争执行
- **必须在同步块中调用**：必须在synchronized方法或块中调用
- **配合wait()使用**：通常与wait()方法配合使用

### notify()和notifyAll()使用示例
```java
public class NotifyExample {
    private final Object lock = new Object();
    private boolean condition = false;
    
    public void waitForCondition() throws InterruptedException {
        synchronized (lock) {
            while (!condition) {
                lock.wait();
            }
            System.out.println("条件满足，继续执行");
        }
    }
    
    public void setCondition() {
        synchronized (lock) {
            condition = true;
            lock.notify(); // 唤醒一个等待线程
        }
    }
    
    public void setConditionAll() {
        synchronized (lock) {
            condition = true;
            lock.notifyAll(); // 唤醒所有等待线程
        }
    }
}
```

## 方法使用最佳实践

### 异常处理
```java
public class ExceptionHandling {
    public void handleInterruptedException() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            // 1. 记录日志
            System.out.println("线程被中断: " + e.getMessage());
            
            // 2. 恢复中断状态
            Thread.currentThread().interrupt();
            
            // 3. 清理资源
            cleanup();
        }
    }
    
    private void cleanup() {
        // 清理资源
    }
}
```

### 避免死锁
```java
public class DeadlockAvoidance {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) {
            synchronized (lock2) {
                // 操作
            }
        }
    }
    
    public void method2() {
        synchronized (lock2) {
            synchronized (lock1) {
                // 操作
            }
        }
    }
}
```

### 性能优化
```java
public class PerformanceOptimization {
    public void optimizeWait() {
        Object lock = new Object();
        boolean condition = false;
        
        synchronized (lock) {
            // 使用while循环而不是if语句
            while (!condition) {
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }
    }
}
```

## 总结

掌握Java线程的基本控制方法是并发编程的基础。wait()、sleep()、yield()、interrupt()等方法各有特点，需要根据具体场景选择合适的方法。在实际开发中，应该注意异常处理、避免死锁、优化性能等方面，编写高质量的并发程序。

—— 完 ——
