---
layout: post
title: RabbitMQ 队列与绑定
subtitle: 队列属性、死信与延迟、绑定键模式
date: 2020-05-15
categories: 技术
tags: RabbitMQ Queue Binding DLX 延迟
cover: 
---

队列承载消息存储与分发，配合绑定键与策略可实现丰富的路由与生命周期控制。

## 队列属性
- durable：Broker 重启后是否保留
- exclusive：仅限当前连接，关闭即删除
- auto-delete：无消费者后自动删除

## 死信与延迟
```java
Map<String, Object> args = new HashMap<>();
args.put("x-dead-letter-exchange", "ex.dlx");
args.put("x-dead-letter-routing-key", "rk.dlx");
args.put("x-message-ttl", 60000); // 延迟/过期
ch.queueDeclare("q.biz", true, false, false, args);
```

## 绑定键模式
- 精确键（Direct）、通配（Topic）、广播（Fanout）

—— 完 ——


