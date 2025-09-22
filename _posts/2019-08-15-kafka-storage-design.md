---
layout: post
title: Kafka 数据存储设计
subtitle: 分区日志、段与索引
date: 2019-08-15
categories: 技术
tags: Kafka 存储 日志 索引
cover: 
---

Kafka 的分区日志以“段（segment）”为单位进行文件滚动，每个段包含数据文件与稀疏索引，借助顺序写与页缓存实现高吞吐。

## 分区数据文件
- 条目结构：offset、timestamp、size、payload
- 以追加方式写入，天然顺序 I/O

## 段与滚动
- 基于大小/时间进行滚动：`log.segment.bytes` / `log.roll.ms`
- 旧段只读，便于删除与压缩策略生效

## 稀疏索引
- offset 索引与时间索引配合，二分查找定位到段内近似位置
- 通过顺序扫描少量数据完成精确定位

## 示例（3个以内）
```bash
# 列出分区目录
ls /kafka-logs/demo-0

# 查看段与索引文件
ls demo-0/00000000000000000000.{log,index,timeindex}
```

—— 完 ——


