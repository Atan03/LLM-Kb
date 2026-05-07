---
title: 数据库事务的 ACID 特性是什么？
category: interview-question
topic: database
subtopics: [transaction, acid, mvcc, logs]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - 事务的特性
summary: 考察事务 ACID 基础，以及 undo log、redo log、锁和 MVCC 的支撑关系。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# 数据库事务的 ACID 特性是什么？

## Variants

- 事务的特性。

## Short Answer

事务的四个特性是 ACID：原子性保证要么全部成功要么全部回滚，通常依赖 undo log；一致性保证事务前后业务约束仍然成立；隔离性解决并发事务互相影响，依赖锁和 MVCC；持久性保证提交后的修改不会因宕机丢失，通常依赖 redo log。

## Full Answer

Atomicity 原子性强调事务内部操作是一个整体。如果中间任何一步失败，数据库要能回滚到事务开始前的状态。以 MySQL InnoDB 为例，回滚能力通常由 undo log 支撑。

Consistency 一致性是事务的目标，而不是某一个单独机制。它要求事务执行前后数据库都满足业务规则和完整性约束，例如余额不能凭空增加、外键约束不能被破坏、状态流转必须合法。

Isolation 隔离性处理并发事务之间的可见性和干扰问题。不同隔离级别会允许或避免脏读、不可重复读、幻读等现象。实现上通常结合锁、MVCC、undo 版本链和 ReadView。

Durability 持久性保证事务提交后，即使数据库崩溃，修改也能恢复。数据库通常先写日志再落盘数据页，InnoDB 中 redo log 是持久性的关键机制。

面试中可以把 ACID 和底层机制一起说，但要注意一致性是最终语义目标，不能简单说“由某个 log 保证”。

## Follow-ups

- MVCC 如何实现读已提交和可重复读？
- undo log 和 redo log 分别解决什么问题？
- 幻读如何解决？

## Related Concepts

- ACID
- undo log
- redo log
- MVCC
- 事务隔离级别

## In Outlines

- [[interview/outlines/database]]
