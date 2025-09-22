---
layout: post
title: Kafka 概念与架构总览
subtitle: 主题、分区、ISR、副本与消息语义
date: 2019-07-15
categories: 技术
tags: Kafka 消息队列 分布式 流处理
cover: 
---

Kafka 是分布式、高吞吐、可持久化的发布/订阅消息系统。核心在于基于磁盘顺序写的分区日志、复制机制与消费位点管理。

## 核心概念
- Topic/Partition：主题由多个分区组成，分区为有序追加日志
- Replica/ISR：每个分区多副本；ISR 集合内副本与 Leader 进度同步
- Offset：分区内消息位点，由消费者组按组维度维护
- Broker/Cluster：broker 为服务进程，多个 broker 构成集群
- Producer/Consumer：生产者按分区策略写入；消费者组做水平扩展与重平衡

## 投递语义
- At most once：最多一次（可能丢）
- At least once：至少一次（可能重复）
- Exactly once：端到端幂等与事务支持（生产者幂等+事务，消费者端幂等处理）

## 最小示例
```java
// 生产者（示例1）
Properties p = new Properties();
p.put("bootstrap.servers", "localhost:9092");
p.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
p.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(p);
producer.send(new ProducerRecord<>("demo", "k", "v"));
producer.flush();
producer.close();
```

```java
// 消费者（示例2）
Properties c = new Properties();
c.put("bootstrap.servers", "localhost:9092");
c.put("group.id", "g1");
c.put("enable.auto.commit", "false");
c.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
c.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(c);
consumer.subscribe(Arrays.asList("demo"));
for (ConsumerRecord<String, String> r : consumer.poll(Duration.ofSeconds(1))) {
  // 处理后提交位点
}
consumer.commitSync();
consumer.close();
```

—— 完 ——


