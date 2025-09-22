---
layout: post
title: Logback 配置示例（精选）
subtitle: 常用场景的最小配置片段
date: 2019-06-07
categories: 技术
tags: 日志 Logback 配置 示例
cover: 
---

精选 3-5 个常用 Logback 片段，便于开箱即用。

## 控制台+文件+按日归档
```xml
<configuration>
  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logs/app.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>
      <maxHistory>7</maxHistory>
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

## 异步输出
```xml
<appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
  <queueSize>8192</queueSize>
  <appender-ref ref="FILE"/>
</appender>
```

## 按业务包单独级别
```xml
<logger name="com.example.dao" level="WARN"/>
<logger name="org.hibernate.SQL" level="INFO"/>
```

—— 完 ——


