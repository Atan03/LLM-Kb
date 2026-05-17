---
title: Database Interview Outline
category: interview-outline
topic: database
summary: Compact study guide for database transaction and locking interview questions.
question_count: 4
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# Database Interview Outline

## Study Guide

数据库事务题的主线可以这样记：事务是数据库给业务的一份承诺，ACID 是这份承诺的四个侧面。面试官通常不是想听四个英文单词，而是想看你能不能把事务语义和数据库底层机制连起来。

原子性说的是“要么都做，要么都不做”，重点可以落到 undo log 和回滚；一致性说的是“事务前后业务规则仍然成立”，它是最终目标，不要硬说由某个单独机制保证；隔离性说的是“并发事务之间不能互相踩脏数据”，可以接到锁、MVCC 和隔离级别；持久性说的是“提交后不能因为宕机丢失”，可以接到 redo log 和崩溃恢复。

事务基础题的答题节奏不要铺太散。先用一句话报出 ACID，再每个特性给一个语义解释和一个底层支撑，最后主动补一句“一致性是业务正确性的目标，其他机制共同服务于它”。这样比机械背诵更像理解过数据库。

## Interview Thread

建议先讲 `[[interview/questions/database-transaction-acid]]` 建立事务语义，再讲 `[[interview/questions/mysql-locking-and-optimistic-vs-pessimistic]]` 落到并发控制策略。后续最容易被追问的是：

- undo log 和 redo log 分别解决什么问题？
- 隔离性为什么需要 MVCC 和锁？
- 不同隔离级别解决哪些读现象？

## Questions

- [[interview/questions/database-transaction-acid]]
- [[interview/questions/mysql-locking-and-optimistic-vs-pessimistic]]
- [[interview/questions/mysql-deep-pagination]]
- [[interview/questions/mysql-slow-query-beyond-index]]

## Key Concepts

- ACID
- Transaction
- undo log
- redo log
- MVCC
- Isolation Level
- Record/Gap/Next-Key Lock
- Optimistic vs Pessimistic Lock
- Deep Pagination
- Covering Index
- Keyset Pagination
- Lock Wait
- MDL Lock
- Connection Pool

## Open Gaps

- 还缺少隔离级别、MVCC 细节、索引、慢 SQL、主从复制和分库分表题。
