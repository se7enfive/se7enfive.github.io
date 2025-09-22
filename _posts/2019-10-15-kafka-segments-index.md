---
layout: post
title: Kafka 段与索引
subtitle: Segment 滚动、时间索引与稀疏定位
date: 2019-10-15
categories: 技术
tags: Kafka 段 索引 稀疏
cover: 
---

Kafka 为每个段维护 offset 索引与时间索引，通过稀疏索引+二分查找快速定位消息近似位置，再顺序扫描少量数据获取精确条目。

## 段滚动策略
- 按大小：log.segment.bytes
- 按时间：log.roll.ms
- 压缩日志下的合并/清理（compaction/cleanup）

## 索引类型
- .index：offset → 文件位置
- .timeindex：timestamp → offset 近似映射

## 命令示例
```bash
ls demo-0/*.index demo-0/*.timeindex
```

—— 完 ——


