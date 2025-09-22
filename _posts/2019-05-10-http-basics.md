---
layout: post
title: HTTP 原理与请求-响应流程
subtitle: 传输流程、状态码要点与HTTPS简述
date: 2019-05-10
categories: 技术
tags: HTTP HTTPS 网络 协议
cover: 
---

HTTP 是无状态的请求/响应协议。本文以最小必需要点梳理流程。

## 传输流程（简版）
1. 解析 URL、DNS 解析主机名得到 IP 与端口
2. TCP 三次握手建立连接（或复用已有连接）
3. 发送请求行/头/可选主体（HTTP/1.1 默认持久连接）
4. 服务器返回状态行/头/主体
5. 连接复用或关闭（Connection: keep-alive / HTTP/2 多路复用）

## 常见状态码速记
- 1xx：信息；2xx：成功（200/201）；3xx：重定向（301/302/304）
- 4xx：客户端错误（400/401/403/404/429）
- 5xx：服务端错误（500/502/503/504）

## HTTPS 要点
- TLS 握手/证书校验/对称密钥协商
- HSTS、ALPN、HTTP/2/3 与 QUIC 简述
- 性能与安全权衡（会话复用、OCSP Stapling）

—— 完 ——


