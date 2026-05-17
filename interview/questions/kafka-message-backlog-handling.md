---
title: 积压消息如何处理？重试、落库暂存还是死信队列？
category: interview-question
topic: mq
subtopics: [kafka, dead-letter-queue, retry, compensation, backpressure]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/kafka-dlq]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 限流产生的积压消息，选择打回重试、落库暂存还是死信队列？各自副作用？
summary: 考察积压消息处理三层策略：本地重试（瞬时故障）→ retry topic 延迟重试（短期故障）→ DLQ+告警+落库备案（永久故障），以及各策略的副作用。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 积压消息如何处理？重试、落库暂存还是死信队列？

## Variants

- 消息消费失败了怎么处理？
- 死信队列是什么？
- 重试死循环怎么避免？
- 落库暂存和死信队列怎么选？

## Short Answer

三种策略各有适用场景：本地重试覆盖瞬时故障（网络抖动），retry topic 做延迟重试（短期故障），DLQ+落库备案处理永久故障。生产推荐三层组合：本地重试3次 → retry topic 延迟重试2次（指数退避） → DLQ + 告警 + 落库人工 review。

## Full Answer

### 打回重试（重新入队）

- 做法：消费失败后重新 produce 到原 topic 或 retry topic
- 适用：瞬时失败，网络抖动、下游短暂不可用
- 副作用：如果问题没解决会形成**死循环**，打满 topic；必须设最大重试次数，超过后转其他策略

### 落库暂存

- 做法：消费失败后写入数据库补偿表，由独立补偿任务定时重跑
- 适用：需要人工介入的场景，或需要业务补偿逻辑（如支付失败需要退款）
- 副作用：引入数据库依赖，补偿任务时效性不如 MQ；落库本身也可能失败，需要保证原子性（和消息确认的先后顺序）

### 死信队列（DLQ）

- 做法：超过最大重试次数后路由到死信 topic，专门消费者或人工处理
- 适用：无法自动恢复的消息，数据格式错误、业务规则校验不通过
- 副作用：DLQ 本身也需要监控和处理，不能成为"垃圾桶"；需要 SLA 规定多长时间内处理完

### 生产推荐：三层组合

```
1. 本地重试 3 次（立即，间隔极短）
2. 投 retry topic 延迟重试 2 次（指数退避：1min, 5min）
3. 进 DLQ + 告警 + 落库备案
```

覆盖了：
- 瞬时故障：本地重试就解决了
- 短期故障：延迟重试等下游恢复
- 永久故障：DLQ + 人工 review，不污染主消费链路

## Follow-ups

- retry topic 和原 topic 怎么区分？
- DLQ 的消息如何重新投回主消费链路？
- 落库和消息确认的原子性怎么保证？

## Related Concepts

- Dead Letter Queue
- Retry Topic
- Compensation Task
- Exponential Backoff
- Message Requeue

## In Outlines

- [[interview/outlines/mq]]
