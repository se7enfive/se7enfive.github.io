---
layout: post
title: RabbitMQ Publisher 关键实践
subtitle: 发布确认、mandatory/return 与幂等
date: 2020-04-15
categories: 技术
tags: RabbitMQ Publisher Confirm 幂等
cover: 
---

Publisher 端的可靠性与幂等至关重要，建议开启 Confirm 模式并妥善处理路由失败回退。

## 发布确认（Confirm）
```java
ch.confirmSelect();
ch.basicPublish("ex", "key", null, data);
ch.waitForConfirmsOrDie();
```

## mandatory 与 Return 回调
```java
ch.addReturnListener((replyCode, replyText, exchange, routingKey, props, body) -> {
  // 处理未被路由到队列的消息
});
ch.basicPublish("ex", "key", true, null, data);
```

## 幂等与重试
- 业务侧去重键；失败重试+死信队列（DLX）
- 超时/异常情况记录与补偿

—— 完 ——


