---
title: MQ Interview Outline
category: interview-outline
topic: mq
summary: Compact study guide for message queue trade-offs in async workflows and long-running task orchestration.
question_count: 5
chapter_count: 0
status: growing
created: 2026-05-09T10:52:46+08:00
updated: 2026-05-12T00:00:00+08:00
---

# MQ Interview Outline

## Study Guide

MQ 题的核心不是背 Kafka 特性，而是讲清“为什么要异步化、异步化带来什么代价、怎么用工程策略把代价控住”。最常见主线是吞吐与延迟权衡：单次链路可能变长，但系统总体吞吐和稳定性更高。

长时任务状态管理则是第二条主线：只靠扫表实时性差、只靠消息链路复杂且补偿困难。更稳的工程实践通常是事件驱动主流程 + 状态落库 + 定时补偿，兼顾实时性与可追溯性。

## Interview Thread

建议先复习 `[[interview/questions/kafka-throughput-vs-latency-tradeoff]]`，讲吞吐/延迟与削峰填谷；再复习 `[[interview/questions/long-running-task-state-polling-vs-mq]]`，讲长时任务状态的架构取舍与混合方案。

## Questions

- [[interview/questions/kafka-throughput-vs-latency-tradeoff]]
- [[interview/questions/long-running-task-state-polling-vs-mq]]
- [[interview/questions/kafka-consumer-idempotency]]
- [[interview/questions/kafka-backpressure-consumer-protection]]
- [[interview/questions/kafka-message-backlog-handling]]

## Key Concepts

- Throughput vs Latency
- Async Decoupling
- Backpressure
- Polling
- Event-driven
- Compensation
- Idempotency
- Kafka Consumer
- Redis Dedup
- Dead Letter Queue
- Retry Topic
- Exponential Backoff
- Consumer Lag

## Open Gaps

- 还缺少分区策略、顺序保证、事务消息、重平衡影响和可观测性指标体系的面经样本。
