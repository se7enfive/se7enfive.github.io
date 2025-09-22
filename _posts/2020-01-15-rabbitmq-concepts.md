---
layout: post
title: RabbitMQ 概念与基础
subtitle: 生产者、交换器、队列、路由键与消费者
date: 2020-01-15
categories: 技术
tags: RabbitMQ 消息队列 MQ 基础
cover: 
---

RabbitMQ 是基于 AMQP 协议的消息中间件，核心由 Exchange（交换器）、Queue（队列）与 Binding（绑定）组成。

## 基本术语
- Producer：生产者，发布消息到交换器
- Exchange：交换器，根据类型与规则路由消息
- Queue：队列，存储消息供消费者拉取
- Binding：交换器与队列的绑定关系，含路由键模式
- Consumer：消费者，从队列消费并确认
- Routing Key：用于路由匹配的键
- vhost：虚拟主机，逻辑隔离命名空间

## 可靠性要点
- 持久化：交换器/队列声明 durable，消息 deliveryMode=2
- 确认：publisher confirms、消费者 ack（autoAck=false）
- 冗余：镜像/队列高可用（基于策略）

## 最小例（Java ≤3）
```java
ConnectionFactory cf = new ConnectionFactory();
cf.setHost("localhost");
try (Connection c = cf.newConnection(); Channel ch = c.createChannel()) {
  ch.queueDeclare("q.demo", true, false, false, null);
  ch.basicPublish("", "q.demo", null, "hello".getBytes());
}
```

```java
try (Connection c = cf.newConnection(); Channel ch = c.createChannel()) {
  ch.basicConsume("q.demo", false, (tag, msg) -> {
    System.out.println(new String(msg.getBody()));
    ch.basicAck(msg.getEnvelope().getDeliveryTag(), false);
  }, tag -> {});
}
```

—— 完 ——


