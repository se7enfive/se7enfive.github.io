---
layout: post
title: RabbitMQ 架构与核心组件
subtitle: Publisher/Exchange/Binding/Queue/Connection/Channel/Consumer/vhost
date: 2020-02-15
categories: 技术
tags: RabbitMQ 架构 组件 MQ
cover: 
---

RabbitMQ 基于 Erlang/OTP，遵循 AMQP。其核心组件协同实现消息路由、存储与消费。

## 组件速览
- Publisher：发布消息
- Exchange：路由消息（Direct/Fanout/Topic/Headers）
- Binding：交换器与队列的绑定关系
- Queue：消息存储与分发
- Connection：TCP 连接
- Channel：复用于连接上的逻辑通道
- Consumer：消费端，支持推/拉与确认
- vhost：命名空间隔离

## 典型流程
Publisher → Exchange → (Binding via routing key) → Queue → Consumer(ack)

## 高可用
- 镜像队列策略（经典模式）或队列仲裁（Quorum Queues）
- 集群节点：Disk/ RAM node；策略控制镜像范围

—— 完 ——


