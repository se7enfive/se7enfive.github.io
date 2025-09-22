---
layout: post
title: ELK 日志平台概览
subtitle: Elasticsearch、Logstash、Kibana 与采集链路
date: 2019-06-14
categories: 技术
tags: 日志 ELK Elasticsearch Logstash Kibana
cover: 
---

ELK（Elasticsearch + Logstash + Kibana）是常见的日志检索与可视化平台，结合 Beats/Fluentd 形成采集链路。

## 组件职责
- Elasticsearch：倒排索引与查询
- Logstash：采集/过滤/转发（管道）
- Kibana：查询与可视化
- Beats：轻量采集器（Filebeat/Metricbeat）

## 典型链路
应用 → Filebeat → Logstash（过滤/解析） → Elasticsearch（索引） → Kibana（查询）

## 最佳实践
- 结构化日志（JSON）便于解析与检索
- 字段规范与索引模板，避免字段爆炸
- 生命周期管理 ILM（热/温/冷）控制成本
- 安全：鉴权、TLS、索引级/字段级权限

—— 完 ——


