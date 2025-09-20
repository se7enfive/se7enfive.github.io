---
layout: post
title: Java线程间数据共享详解
subtitle: 线程间数据共享的方式、机制与最佳实践
date: 2018-11-02
categories: 技术
tags: java 线程 数据共享 并发 同步
cover: 
---

线程间数据共享是Java并发编程中的核心问题，理解不同的共享方式、同步机制和最佳实践对于编写正确的并发程序至关重要。

## 线程间数据共享概述

### 数据共享的方式
- **共享内存**：通过共享变量进行数据交换
- **消息传递**：通过队列、管道等机制传递数据
- **回调机制**：通过回调函数传递数据
- **事件机制**：通过事件监听器传递数据

### 数据共享的挑战
- **可见性问题**：一个线程的修改对其他线程不可见
- **原子性问题**：复合操作可能被中断
- **有序性问题**：指令重排序可能导致意外结果
- **死锁问题**：多个线程相互等待

## 共享内存方式

### 1. 使用volatile关键字
```java
public class VolatileDataSharing {
    private volatile boolean flag = false;
    private volatile int counter = 0;
    
    public void demonstrateVolatileSharing() {
        // 线程1：设置数据
        Thread thread1 = new Thread(() -> {
            try {
                Thread.sleep(1000);
                flag = true;
                counter = 100;
                System.out.println("线程1设置数据完成");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        // 线程2：读取数据
        Thread thread2 = new Thread(() -> {
            while (!flag) {
                // 等待标志位
            }
            System.out.println("线程2读取到数据: " + counter);
        });
        
        thread1.start();
        thread2.start();
    }
}
```

### 2. 使用synchronized关键字
```java
public class SynchronizedDataSharing {
    private int sharedData = 0;
    private final Object lock = new Object();
    
    public void demonstrateSynchronizedSharing() {
        // 线程1：写入数据
        Thread thread1 = new Thread(() -> {
            synchronized (lock) {
                sharedData = 42;
                System.out.println("线程1写入数据: " + sharedData);
            }
        });
        
        // 线程2：读取数据
        Thread thread2 = new Thread(() -> {
            synchronized (lock) {
                System.out.println("线程2读取数据: " + sharedData);
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

### 3. 使用ReentrantLock
```java
public class ReentrantLockDataSharing {
    private int sharedData = 0;
    private final ReentrantLock lock = new ReentrantLock();
    
    public void demonstrateReentrantLockSharing() {
        // 线程1：写入数据
        Thread thread1 = new Thread(() -> {
            lock.lock();
            try {
                sharedData = 42;
                System.out.println("线程1写入数据: " + sharedData);
            } finally {
                lock.unlock();
            }
        });
        
        // 线程2：读取数据
        Thread thread2 = new Thread(() -> {
            lock.lock();
            try {
                System.out.println("线程2读取数据: " + sharedData);
            } finally {
                lock.unlock();
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

## 消息传递方式

### 1. 使用BlockingQueue
```java
public class BlockingQueueDataSharing {
    private final BlockingQueue<String> messageQueue = new LinkedBlockingQueue<>();
    
    public void demonstrateBlockingQueueSharing() {
        // 生产者线程
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    String message = "消息-" + i;
                    messageQueue.put(message);
                    System.out.println("生产者发送: " + message);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        // 消费者线程
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    String message = messageQueue.take();
                    System.out.println("消费者接收: " + message);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

### 2. 使用ConcurrentLinkedQueue
```java
public class ConcurrentLinkedQueueDataSharing {
    private final ConcurrentLinkedQueue<String> messageQueue = new ConcurrentLinkedQueue<>();
    
    public void demonstrateConcurrentLinkedQueueSharing() {
        // 生产者线程
        Thread producer = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                String message = "消息-" + i;
                messageQueue.offer(message);
                System.out.println("生产者发送: " + message);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });
        
        // 消费者线程
        Thread consumer = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                String message = messageQueue.poll();
                if (message != null) {
                    System.out.println("消费者接收: " + message);
                }
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

### 3. 使用Exchanger
```java
public class ExchangerDataSharing {
    private final Exchanger<String> exchanger = new Exchanger<>();
    
    public void demonstrateExchangerSharing() {
        // 线程1：发送数据
        Thread thread1 = new Thread(() -> {
            try {
                String data = "线程1的数据";
                System.out.println("线程1发送: " + data);
                String received = exchanger.exchange(data);
                System.out.println("线程1接收: " + received);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        // 线程2：接收数据
        Thread thread2 = new Thread(() -> {
            try {
                String data = "线程2的数据";
                System.out.println("线程2发送: " + data);
                String received = exchanger.exchange(data);
                System.out.println("线程2接收: " + received);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

## 回调机制

### 1. 使用回调接口
```java
public class CallbackDataSharing {
    public interface DataCallback {
        void onDataReceived(String data);
    }
    
    private DataCallback callback;
    
    public void setCallback(DataCallback callback) {
        this.callback = callback;
    }
    
    public void demonstrateCallbackSharing() {
        // 设置回调
        setCallback(data -> {
            System.out.println("回调接收数据: " + data);
        });
        
        // 工作线程
        Thread worker = new Thread(() -> {
            try {
                Thread.sleep(1000);
                String data = "工作线程的数据";
                if (callback != null) {
                    callback.onDataReceived(data);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        worker.start();
    }
}
```

### 2. 使用CompletableFuture
```java
public class CompletableFutureDataSharing {
    public void demonstrateCompletableFutureSharing() {
        // 异步任务
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(1000);
                return "异步任务的结果";
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                return null;
            }
        });
        
        // 处理结果
        future.thenAccept(result -> {
            System.out.println("接收结果: " + result);
        });
        
        // 等待完成
        try {
            String result = future.get();
            System.out.println("最终结果: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

## 事件机制

### 1. 使用观察者模式
```java
public class ObserverDataSharing {
    public interface DataObserver {
        void onDataChanged(String data);
    }
    
    private final List<DataObserver> observers = new ArrayList<>();
    private String data;
    
    public void addObserver(DataObserver observer) {
        observers.add(observer);
    }
    
    public void setData(String data) {
        this.data = data;
        notifyObservers(data);
    }
    
    private void notifyObservers(String data) {
        for (DataObserver observer : observers) {
            observer.onDataChanged(data);
        }
    }
    
    public void demonstrateObserverSharing() {
        // 添加观察者
        addObserver(data -> {
            System.out.println("观察者1接收数据: " + data);
        });
        
        addObserver(data -> {
            System.out.println("观察者2接收数据: " + data);
        });
        
        // 设置数据
        setData("新数据");
    }
}
```

### 2. 使用EventBus
```java
public class EventBusDataSharing {
    private final EventBus eventBus = new EventBus();
    
    public void demonstrateEventBusSharing() {
        // 注册监听器
        eventBus.register(new Object() {
            @Subscribe
            public void handleDataEvent(DataEvent event) {
                System.out.println("监听器接收数据: " + event.getData());
            }
        });
        
        // 发布事件
        Thread publisher = new Thread(() -> {
            try {
                Thread.sleep(1000);
                DataEvent event = new DataEvent("事件数据");
                eventBus.post(event);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        publisher.start();
    }
    
    static class DataEvent {
        private final String data;
        
        public DataEvent(String data) {
            this.data = data;
        }
        
        public String getData() {
            return data;
        }
    }
}
```

## 原子操作

### 1. 使用AtomicInteger
```java
public class AtomicIntegerDataSharing {
    private final AtomicInteger counter = new AtomicInteger(0);
    
    public void demonstrateAtomicIntegerSharing() {
        // 多个线程同时操作
        for (int i = 0; i < 5; i++) {
            Thread thread = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter.incrementAndGet();
                }
            });
            thread.start();
        }
        
        // 等待所有线程完成
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        System.out.println("最终计数: " + counter.get());
    }
}
```

### 2. 使用AtomicReference
```java
public class AtomicReferenceDataSharing {
    private final AtomicReference<String> data = new AtomicReference<>("初始值");
    
    public void demonstrateAtomicReferenceSharing() {
        // 线程1：更新数据
        Thread thread1 = new Thread(() -> {
            String newData = "线程1的数据";
            data.set(newData);
            System.out.println("线程1设置数据: " + newData);
        });
        
        // 线程2：读取数据
        Thread thread2 = new Thread(() -> {
            String currentData = data.get();
            System.out.println("线程2读取数据: " + currentData);
        });
        
        thread1.start();
        thread2.start();
    }
}
```

## 线程本地存储

### 1. 使用ThreadLocal
```java
public class ThreadLocalDataSharing {
    private final ThreadLocal<String> threadLocalData = new ThreadLocal<>();
    
    public void demonstrateThreadLocalSharing() {
        // 线程1：设置数据
        Thread thread1 = new Thread(() -> {
            threadLocalData.set("线程1的数据");
            System.out.println("线程1设置数据: " + threadLocalData.get());
        });
        
        // 线程2：设置数据
        Thread thread2 = new Thread(() -> {
            threadLocalData.set("线程2的数据");
            System.out.println("线程2设置数据: " + threadLocalData.get());
        });
        
        thread1.start();
        thread2.start();
    }
}
```

### 2. 使用InheritableThreadLocal
```java
public class InheritableThreadLocalDataSharing {
    private final InheritableThreadLocal<String> inheritableThreadLocal = new InheritableThreadLocal<>();
    
    public void demonstrateInheritableThreadLocalSharing() {
        // 父线程设置数据
        inheritableThreadLocal.set("父线程数据");
        System.out.println("父线程设置数据: " + inheritableThreadLocal.get());
        
        // 子线程继承数据
        Thread childThread = new Thread(() -> {
            System.out.println("子线程继承数据: " + inheritableThreadLocal.get());
        });
        
        childThread.start();
    }
}
```

## 最佳实践

### 1. 选择合适的数据共享方式
```java
public class DataSharingBestPractices {
    public void demonstrateBestPractices() {
        // 1. 简单状态标志 -> volatile
        volatile boolean flag = false;
        
        // 2. 复杂状态 -> synchronized
        synchronized (this) {
            // 复杂操作
        }
        
        // 3. 高性能计数 -> AtomicInteger
        AtomicInteger counter = new AtomicInteger(0);
        
        // 4. 线程间通信 -> BlockingQueue
        BlockingQueue<String> queue = new LinkedBlockingQueue<>();
        
        // 5. 线程本地数据 -> ThreadLocal
        ThreadLocal<String> threadLocal = new ThreadLocal<>();
    }
}
```

### 2. 避免常见错误
```java
public class CommonMistakes {
    public void demonstrateCommonMistakes() {
        // 错误1：认为volatile保证原子性
        volatile int count = 0;
        count++; // 不是原子操作
        
        // 错误2：过度使用synchronized
        synchronized (this) {
            // 不必要的同步
        }
        
        // 错误3：忘记释放锁
        ReentrantLock lock = new ReentrantLock();
        lock.lock();
        // 忘记在finally中释放锁
        
        // 错误4：使用ThreadLocal忘记清理
        ThreadLocal<String> threadLocal = new ThreadLocal<>();
        threadLocal.set("数据");
        // 忘记调用threadLocal.remove()
    }
}
```

### 3. 性能优化
```java
public class PerformanceOptimization {
    public void demonstratePerformanceOptimization() {
        // 1. 减少锁的粒度
        private final Object lock1 = new Object();
        private final Object lock2 = new Object();
        
        // 2. 使用无锁数据结构
        ConcurrentHashMap<String, String> map = new ConcurrentHashMap<>();
        AtomicInteger counter = new AtomicInteger(0);
        
        // 3. 使用局部变量
        int localValue = 0;
        for (int i = 0; i < 1000; i++) {
            localValue += i;
        }
        
        // 4. 批量操作
        List<String> batch = new ArrayList<>();
        // 批量处理
    }
}
```

## 总结

线程间数据共享是Java并发编程中的核心问题，需要根据具体场景选择合适的数据共享方式。通过理解不同的同步机制、消息传递方式和最佳实践，可以编写出正确、高效的并发程序。

—— 完 ——
