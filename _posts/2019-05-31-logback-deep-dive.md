---
layout: post
title: Logback 深入解析
subtitle: 配置结构、异步化与归档策略
date: 2019-05-31
categories: 技术
tags: 日志 Logback SLF4J 配置
cover: 
---

Logback 为 SLF4J 的原生实现，性能与特性均优于 Log4j（1.x）。

## 配置结构（logback.xml）
```xml
<configuration>
  <property name="LOG_HOME" value="logs"/>

  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{ISO8601} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOG_HOME}/app.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>${LOG_HOME}/app.%d{yyyy-MM-dd}.log.gz</fileNamePattern>
      <maxHistory>14</maxHistory>
    </rollingPolicy>
    <encoder>
      <pattern>%d %-5level [%thread] %logger - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="INFO">
    <appender-ref ref="CONSOLE"/>
    <appender-ref ref="FILE"/>
  </root>
</configuration>
```

## 异步化
```xml
<appender name="ASYNC_FILE" class="ch.qos.logback.classic.AsyncAppender">
  <appender-ref ref="FILE"/>
  <queueSize>8192</queueSize>
  <discardingThreshold>0</discardingThreshold>
</appender>
```

## 实战要点
- profile 分环境外置 `logback-spring.xml`
- 按业务包设置不同级别；对SQL单独降噪
- 大流量服务默认启用异步Appender，避免阻塞

—— 完 ——

