---
layout: post
title: Log4j 配置与最佳实践
subtitle: Appender/Logger/Pattern 与常见坑
date: 2019-05-24
categories: 技术
tags: 日志 Log4j 配置 实践
cover: 
---

本文简述 Log4j 的核心概念与常用配置，并给出实践要点。

## 核心概念
- Logger：按层级组织的日志记录器
- Appender：输出目标（Console、RollingFile、Socket）
- Layout/Pattern：输出格式

## 基础配置示例（properties）
```properties
log4j.rootLogger=INFO, CONSOLE, FILE

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-5p %c{1.} - %m%n

log4j.appender.FILE=org.apache.log4j.RollingFileAppender
log4j.appender.FILE.File=logs/app.log
log4j.appender.FILE.MaxFileSize=20MB
log4j.appender.FILE.MaxBackupIndex=10
log4j.appender.FILE.layout=org.apache.log4j.PatternLayout
log4j.appender.FILE.layout.ConversionPattern=%d %-5p [%t] %c - %m%n
```

## 最佳实践
- 业务与框架分级：包级别单独下调/上调
- 滚动策略：容量或时间滚动，避免单文件过大
- 脱敏：对PII进行掩码；避免日志泄露密钥
- 性能：异步/缓冲输出；避免大量字符串拼接

—— 完 ——


