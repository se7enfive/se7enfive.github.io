---
layout: post
title: Zookeeper 角色：Leader / Follower / Observer
subtitle: 集群角色分工与读写流程
date: 2019-06-28
categories: 技术
tags: Zookeeper 角色 Leader Follower Observer
cover: 
---

Zookeeper 集群包含三种主要角色：Leader、Follower、Observer。它们共同保证高可用与一致性。

## 角色职责
- Leader：处理写请求、负责事务提案与提交（ZAB 协议），对读也可响应
- Follower：参与投票（选主/提交）、处理读请求、转发写给 Leader
- Observer：仅提供读能力，不参与多数投票，适合横向扩展读

## 写请求流程（简述）
1. 客户端向任意节点发送写请求
2. 非 Leader 转发给 Leader
3. Leader 生成提案（proposal）并广播
4. 过半 Follower ACK 后，Leader 提交并通知集群
5. Leader/Followers 应用事务并向客户端返回

## 读请求流程
- 直接在本地读取；为线性一致读可先 `sync` 与 Leader 同步 zxid 进度

## 实战建议
- 写多读多：优先提高 Follower 数量，Observer 增强读扩展
- 跨机房：Observer 放在远端，避免写多数跨区

—— 完 ——


