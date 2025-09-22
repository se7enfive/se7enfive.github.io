---
layout: post
title: TCP/IP 原理速览
subtitle: 四层模型、关键协议、端到端传输与应用映射
date: 2019-04-26
categories: 技术
tags: TCPIP 网络 协议 栈
cover: 
---

TCP/IP 并非单个协议，而是协议族与分层架构的统称，工程实践以其为主流。

## 四层模型
- 网络接口层：以太网、WIFI、PPP
- 网际层（IP）：IP、ICMP、IGMP、ARP/NDP
- 传输层：TCP、UDP（端到端）
- 应用层：HTTP、DNS、SMTP、FTP、SSH

## 关键机制（要点）
- IP 分片与路径 MTU 发现（PMTUD）
- ICMP 用于差错报告与诊断（ping/traceroute）
- TCP 可靠传输：序号/确认、重传、滑动窗口、拥塞控制（慢启动、拥塞避免、快重传/快恢复）
- UDP 无连接、首部小，常用于实时/多媒体/简易RPC

## 小示例（≤3）
- 抓包观察三次握手/HTTP 请求响应
- 使用 traceroute 分析路径与 TTL 递减
- 对比 TCP 与 UDP 的首部字段与使用场景

—— 完 ——


