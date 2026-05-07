---
title: Database Interview Outline
category: interview-outline
topic: database
summary: Lecture-style revision outline for database transaction fundamentals.
question_count: 1
chapter_count: 3
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# Database Interview Outline

## Why This Topic Matters

数据库基础题经常用 ACID 开场，再追问隔离级别、MVCC、undo log、redo log 和锁。一个好的回答不能只背四个单词，要能把语义目标和底层机制连起来。

## Chapter 1. ACID Is The Contract

原子性保证事务要么全成功要么全回滚；一致性保证业务约束在事务前后成立；隔离性控制并发事务之间的互相影响；持久性保证提交后的修改能在故障后恢复。

### Questions

- [[interview/questions/database-transaction-acid]]

### Concepts

- ACID
- Transaction
- MVCC
- undo log
- redo log

## Chapter 2. Mechanism Mapping

undo log 常用于支持回滚和历史版本，redo log 支持崩溃恢复，锁和 MVCC 支撑隔离性。需要注意，一致性是最终目标，不应该简单归因给某一个日志。

## Chapter 3. Interview Answer Pattern

回答事务特性时，可以先一句话讲 ACID，再逐个映射机制，最后主动引出隔离级别和 MVCC。这样能自然承接面试官追问。

## Open Gaps

- 缺少索引、隔离级别、MVCC 细节、锁、主从复制、分库分表和慢 SQL 题。
