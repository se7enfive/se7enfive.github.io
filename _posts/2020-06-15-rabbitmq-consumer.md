---
layout: post
title: RabbitMQ Consumer 关键实践
subtitle: 手动确认、Qos 限流与并发模型
date: 2020-06-15
categories: 技术
tags: RabbitMQ Consumer ack Qos 并发
cover: 
---

消费端的确认与并发控制直接影响吞吐与稳定性，应显式管理 ack 与 preftech。

## 手动确认
```java
ch.basicConsume("q.biz", false, (tag, msg) -> {
  try {
    // 处理…
    ch.basicAck(msg.getEnvelope().getDeliveryTag(), false);
  } catch (Exception ex) {
    ch.basicNack(msg.getEnvelope().getDeliveryTag(), false, true); // 重新入队
  }
}, tag -> {});
```

## Qos 限流
```java
ch.basicQos(100); // 每次最多未确认100条
```

## 并发模型
- 多 Channel、多线程；或使用消费者容器（Spring AMQP）

—— 完 ——


