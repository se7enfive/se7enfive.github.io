---
layout: post
title: Zookeeper 概念与应用场景
subtitle: 一致性协调服务、数据模型与典型用法
date: 2019-06-21
categories: 技术
tags: Zookeeper 分布式 一致性 协调
cover: 
---

Zookeeper 是一个开源的分布式协调服务，为分布式系统提供一致性原语（命名、配置、选主、分布式锁等）。其核心是以强一致（顺序一致）为目标的复制状态机，面向小数据、读多写少的场景。

## 核心特性
- 顺序一致：同一客户端的更新按其发起顺序生效
- 原子性：更新要么成功要么失败，不会部分完成
- 单一系统镜像：客户端无论连到哪个副本，都看到一致视图
- 可靠性：一旦更新提交则不会丢失（多数写）
- 及时性：在一定时间内读到最新已提交数据

## 数据模型
- 层级命名空间，节点称为 Znode，类似文件系统
- Znode 可持久或临时（会话结束自动删除），可顺序节点
- 数据量小（KB 级），适合存放元数据而非大对象

## 典型应用
- 服务注册/发现：服务实例在 `/services/x` 下注册临时顺序节点
- 主从选举：多个节点在同一路径下创建顺序节点，比序号最小者为 Leader
- 分布式锁/队列：基于顺序节点与 Watcher 构造互斥与协调
- 配置中心：小配置推送与监听

## 轻量示例（Curator）
```java
// 连接与基础操作（示例1）
CuratorFramework client = CuratorFrameworkFactory.newClient(
    "localhost:2181", new ExponentialBackoffRetry(1000, 3));
client.start();
client.create().creatingParentsIfNeeded().forPath("/app/config", "v1".getBytes());
byte[] data = client.getData().forPath("/app/config");

// Watcher 监听（示例2）
PathChildrenCache cache = new PathChildrenCache(client, "/services/user", true);
cache.getListenable().addListener((c, e) -> {
  System.out.println("event:" + e.getType());
});
cache.start();

// 临时顺序节点（示例3）
client.create().withMode(CreateMode.EPHEMERAL_SEQUENTIAL)
      .forPath("/election/candidate-", "node".getBytes());
```

## 注意事项
- 仅存小数据；写放大与 Watch 风暴要控制
- 合理设置会话/心跳（tickTime, sessionTimeout）
- 读多写少，热点路径做分层与限流

—— 完 ——


