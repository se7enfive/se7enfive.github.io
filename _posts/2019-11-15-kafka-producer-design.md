---
layout: post
title: Kafka 生产者设计
subtitle: 分区策略、批次与压缩
date: 2019-11-15
categories: 技术
tags: Kafka 生产者 分区 批量 压缩
cover: 
---

生产者通过 RecordAccumulator 批量聚合，Sender 线程异步发送，分区器决定目标分区；压缩与幂等/事务提升可靠性与语义。

## 分区策略
- 轮询：均衡写入
- key hash：相同 key 落到同一分区，利于局部有序
- 自定义：基于负载或热点分配

## 批量与缓冲
- batch.size / linger.ms 共同决定批次
- 增大吞吐、降低单条开销

## 压缩
- 支持 gzip/snappy/lz4/zstd
- 端到端压缩，broker 侧按段写入

## 代码片段（≤3）
```java
p.put("acks", "all");
p.put("enable.idempotence", true);
p.put("compression.type", "lz4");
```

—— 完 ——


