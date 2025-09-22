---
layout: post
title: RabbitMQ 交换器类型
subtitle: Direct / Fanout / Topic / Headers
date: 2020-03-15
categories: 技术
tags: RabbitMQ Exchange 路由
cover: 
---

交换器决定消息如何路由到队列。常见类型：Direct、Fanout、Topic、Headers。

## Direct（精确路由）
- routing key 完全匹配到绑定

## Fanout（广播）
- 忽略 routing key，广播到所有绑定队列

## Topic（通配）
- `*` 匹配一个单词，`#` 匹配零或多个单词

## Headers（头匹配）
- 按消息头键值匹配，`x-match` 为 any/all

## 代码片段（≤3）
```java
ch.exchangeDeclare("ex.topic", BuiltinExchangeType.TOPIC, true);
ch.queueBind("q.order", "ex.topic", "order.*");
```

```java
ch.exchangeDeclare("ex.fanout", BuiltinExchangeType.FANOUT, true);
ch.queueBind("q.broadcast", "ex.fanout", "");
```

—— 完 ——


