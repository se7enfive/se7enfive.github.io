---
layout: post
title: Kafka 分区数据文件详解
subtitle: offset、MessageSize 与数据体
date: 2019-09-15
categories: 技术
tags: Kafka 分区 文件 结构
cover: 
---

分区数据文件是 Kafka 存储的基本单位，采用顺序追加写入，条目带有 offset 与长度，便于快速定位与截断。

## 条目基本结构
- offset：分区内单调递增位点
- size：消息大小
- payload：序列化后的消息内容

## 示例
```bash
# 使用 kafka-dump-log 工具查看
kafka-dump-log --files demo-0/00000000000000000000.log --print-data-log
```

## 截断与保留
- 基于位点或时间/大小策略清理
- 事务与幂等下的截断需考虑高水位（HW）

—— 完 ——


