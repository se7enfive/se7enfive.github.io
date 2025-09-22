---
layout: post
title: SLF4J 基础与用法
subtitle: 统一日志门面、常用API与绑定实现
date: 2019-05-17
categories: 技术
tags: 日志 SLF4J Logback Log4j
cover: 
---

SLF4J（Simple Logging Facade for Java）是日志门面，为上层提供统一API；底层可绑定Logback、Log4j等实现。

## 为什么使用SLF4J
- 解耦：业务仅依赖门面接口，便于替换底层实现
- 统一：跨库/模块统一日志API，避免实现冲突
- 轻量：仅少量接口与适配

## 快速使用
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class DemoService {
  private static final Logger log = LoggerFactory.getLogger(DemoService.class);

  public String hello(String name) {
    log.info("say hello to {}", name);
    try {
      return "Hello, " + name;
    } catch (Exception e) {
      log.error("failed:", e);
      throw e;
    }
  }
}
```

## 常见绑定方式（择一）
- Logback: 引入 `logback-classic`
- Log4j2: 引入 `log4j-slf4j-impl`
- JUL: 引入 `jul-to-slf4j` 并桥接

## 占位符与性能
- 使用 `{}` 占位避免字符串拼接开销
- `log.isDebugEnabled()` 保护昂贵计算

—— 完 ——


