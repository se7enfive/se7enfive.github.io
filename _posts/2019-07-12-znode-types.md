---
layout: post
title: Znode 节点类型与使用
subtitle: 持久/临时/顺序节点与典型模式
date: 2019-07-12
categories: 技术
tags: Zookeeper Znode 节点 模式
cover: 
---

Znode 是 Zookeeper 的数据单元，支持多种类型与 Watch 机制，用于构建丰富的分布式原语。

## 节点类型
- 持久（PERSISTENT）：默认类型，显式删除才消失
- 临时（EPHEMERAL）：会话结束自动删除，不可有子节点
- 顺序（SEQUENTIAL）：创建时自动追加单调序号
- 组合：PERSISTENT_SEQUENTIAL / EPHEMERAL_SEQUENTIAL

## 常见模式
- 服务注册：`/services/x/instance-` 使用 EPHEMERAL_SEQUENTIAL
- 选主：在 `/election/` 下创建顺序节点，序号最小者为主
- 分布式锁：最低序号持锁，其余监听前驱节点删除事件

## 轻量示例（3个以内）
```java
// 创建持久节点
client.create().creatingParentsIfNeeded().forPath("/app/config", "v1".getBytes());

// 创建临时顺序节点
client.create().withMode(CreateMode.EPHEMERAL_SEQUENTIAL)
      .forPath("/lock/mutex-", new byte[0]);

// 监听子节点变化
PathChildrenCache cache = new PathChildrenCache(client, "/services/x", true);
cache.start();
```

—— 完 ——


