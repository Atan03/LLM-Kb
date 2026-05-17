---
title: Kafka 消费如何保证幂等性？
category: interview-question
topic: mq
subtopics: [kafka, idempotency, consumer, dedup, redis]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/kafka-idempotent-consumer]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 发短信业务中，如何利用 Kafka 保证消费幂等性（不重复发短信）？
summary: 考察 Kafka 重复消费场景（rebalance/crash/重试）下的幂等性保证方案：去重表+唯一ID+Redis原子操作+失败回滚。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# Kafka 消费如何保证幂等性？

## Variants

- Kafka 重复消费的原因是什么？
- 发短信怎么保证不重复发？
- 业务 ID 和 offset 哪个更适合做幂等键？

## Short Answer

Kafka 会在 Consumer rebalance、commit offset 前 crash、自动重试时重复投递。幂等性的核心是"同一条消息消费多次，效果和消费一次一样"。方案：消费前用 Redis `setIfAbsent`（原子操作）做去重，结合业务生成的唯一 `messageId`，发送失败时删除 Redis key 让重试可以进来，发送成功后保留足够长时间覆盖重试窗口。

## Full Answer

### Kafka 重复投递的原因

1. **Consumer rebalance**：consumer  group 里某个 consumer 挂了，触发 rebalance，重新投递
2. **commit offset 前 crash**：消费完还没 commit 就挂了，下次 rebalance 会重复投递
3. **自动重试**：消费异常后 Kafka 自动重试

### 幂等性方案：去重表 + 唯一 ID

```java
@KafkaListener(topics = "sms-topic")
public void consume(ConsumerRecord<String, SmsMessage> record) {
    SmsMessage msg = record.value();
    String messageId = msg.getMessageId(); // 业务方生成的唯一 ID

    // 1. Redis 去重（原子操作，性能好）
    Boolean isNew = redisTemplate.opsForValue()
        .setIfAbsent("sms:sent:" + messageId, "1", 24, TimeUnit.HOURS);

    if (Boolean.FALSE.equals(isNew)) {
        log.info("重复消息，跳过: {}", messageId);
        return; // 幂等返回
    }

    try {
        // 2. 发短信
        smsService.send(msg.getPhone(), msg.getContent());

        // 3. 落库记录（持久化，Redis 过期后兜底）
        smsRecordDao.insert(new SmsRecord(messageId, msg.getPhone(), "SUCCESS"));
    } catch (Exception e) {
        // 发送失败，删除 Redis key，允许重试
        redisTemplate.delete("sms:sent:" + messageId);
        throw e; // 抛出让 Kafka 重试
    }
}
```

### 关键设计点

- **`messageId` 必须由业务方生成**：不能用 Kafka offset，因为同一业务消息 rebalance 后 offset 可能不同
- **`setIfAbsent` 是原子操作**：保证并发安全，多个线程同时消费同一消息只有一个能写入成功
- **发送失败要删除 Redis key**：让重试可以进来
- **发送成功后 Redis key 保留足够长**：至少覆盖 Kafka 最大重试窗口（通常 24h）

### 额外兜底：数据库记录

Redis 作为高性能去重层，但 Redis 可能因内存压力删除 key（即使设了 24h TTL）。落库作为持久化兜底：消费成功后在数据库也记录 `messageId`，下次消费前先查库做二次确认。

## Follow-ups

- `messageId` 生成有什么规范？
- Redis 和数据库双重去重会不会影响性能？
- 如果 Redis 挂了怎么办？

## Related Concepts

- Kafka Consumer
- Idempotency
- Redis setIfAbsent
- Consumer Rebalance
- Dead Letter Queue

## In Outlines

- [[interview/outlines/mq]]
