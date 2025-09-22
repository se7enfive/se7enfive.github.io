---
layout: post
title: ZAB 原子广播协议（Zookeeper）
subtitle: 选主、广播与崩溃恢复
date: 2019-07-05
categories: 技术
tags: Zookeeper ZAB 一致性 原子广播
cover: 
---

ZAB（Zookeeper Atomic Broadcast）是 Zookeeper 的一致性协议，保证事务的顺序广播与崩溃恢复。

## 核心阶段
- 选主（Leader Election）：基于 zxid/epoch 的投票选出新 Leader
- 发现（Discovery）：Follower 与 Leader 对齐 epoch
- 同步（Synchronization）：追赶最新提交事务
- 广播（Broadcast）：主从达成过半提交的原子广播

## ZXID 与 Epoch
- zxid = (epoch << 32) | counter
- Leader 更迭提升 epoch；每个事务递增 counter

## 提交流程（写）
1. Leader 生成 proposal（含 zxid）
2. 广播给集群，收集过半 ACK
3. 达到多数后发出 commit，所有副本应用

## 故障与恢复
- Leader 失联：触发选主；Follower 回退未提交事务
- 脑裂处理：以 epoch/zxid 最大者为候选，满足多数

—— 完 ——


