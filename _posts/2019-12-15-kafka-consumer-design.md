---
layout: post
title: Kafka 消费者设计
subtitle: 组协调、重平衡与位点提交
date: 2019-12-15
categories: 技术
tags: Kafka 消费者 位点 重平衡 协调器
cover: 
---

消费者通过组协调器管理分区分配，重平衡确保组内成员与分区映射一致；位点提交决定处理语义。

## 组协调与分区分配
- Range / RoundRobin / Sticky 策略
- 协调器选举组 Leader 进行分配

## 重平衡触发
- 成员加入/离开、订阅主题变更、分区数改变
- 心跳超时导致成员失活

## 位点提交
- 自动/手动；同步/异步
- 失败重试与幂等处理

## 示例（≤3）
```java
c.put("enable.auto.commit", false);
// 处理完一批后
consumer.commitSync();
```

—— 完 ——


