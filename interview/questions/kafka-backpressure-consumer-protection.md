---
title: 消费速度超过下游承载能力时如何保护下游同时保证消息不丢失？
category: interview-question
topic: mq
subtopics: [kafka, backpressure, rate-limiter, consumer, pause-resume]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/kafka-backpressure]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 消费速度超过下游承载能力时，如何保护下游同时保证消息不丢失？
summary: 考察 Kafka 背压保护策略：Consumer 端限速、动态调整消费并发、下游熔断配合 PausePartition/ResumePartition，以及消息留 Kafka 比留内存更安全的核心原则。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 消费速度超过下游承载能力时如何保护下游同时保证消息不丢失？

## Variants

- Kafka 消费端怎么限流？
- 下游扛不住时怎么保护？
- PausePartition 和 ResumePartition 怎么用？

## Short Answer

这是背压问题。核心原则：消息留在 Kafka 里比留在消费者内存队列里安全得多。方案：Consumer 端令牌桶限速控制消费速率、动态调整消费并发、下游异常时用 `PausePartition` 暂停消费，`ResumePartition` 恢复。

## Full Answer

### 方案一：Consumer 端限速

用令牌桶限流，控制消费速率不超过下游能承受的上限：

```java
private final RateLimiter rateLimiter = RateLimiter.create(100); // 每秒 100 个

@KafkaListener(topics = "xxx")
public void consume(ConsumerRecord<?, ?> record) {
    rateLimiter.acquire(); // 令牌不够就阻塞，不是丢弃
    // 消费逻辑...
}
```

限速的代价是 Kafka partition 里消息积压，但消息不丢失——等下游恢复后再消费，积压会慢慢消化。

### 方案二：动态调整消费并发

减少 consumer 线程数或 partition 数，降低并发压力。结合监控（下游 RT、错误率）动态调整。

### 方案三：熔断 + 消息暂存

下游不可用时，不 commit offset，Kafka 下次 poll 会重新投递。用 `PausePartition`/`ResumePartition` 控制节奏：

```java
// 下游异常时暂停消费
consumer.pause(consumer.assignment());
// 下游恢复后继续
consumer.resume(consumer.assignment());
```

### 核心原则

> **消息留在 Kafka 里比留在消费者内存队列里安全得多**

Kafka 本身是持久化的消息暂存层。利用好 offset 管理，消息就不会丢。内存队列（`LinkedBlockingQueue`）如果满了要么丢弃消息，要么撑爆内存——都比不上 Kafka 的可靠性。

## Follow-ups

- 限速和暂停消费哪个更好？
- 如何判断下游已经恢复？
- 积压消息多久能消化完怎么估算？

## Related Concepts

- Backpressure
- Rate Limiter
- Kafka Consumer
- PausePartition / ResumePartition
- Consumer Lag

## In Outlines

- [[interview/outlines/mq]]
