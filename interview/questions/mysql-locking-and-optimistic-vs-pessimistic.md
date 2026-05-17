---
title: MySQL 锁体系，以及乐观锁和悲观锁如何取舍？
category: interview-question
topic: database
subtopics: [mysql-locks, record-lock, gap-lock, next-key-lock, optimistic-lock, pessimistic-lock]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Obsidian/LLM-Kb/interview/sources/20260509-快手-agent后端技术面.md
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260509-快手-agent开发.md
source_questions:
  - mysql 的锁有哪些?
  - 讲一下乐观锁和悲观锁
summary: 考察 InnoDB 锁粒度/锁模式及并发控制策略（乐观锁与悲观锁）的业务取舍。
created: 2026-05-09T11:17:19+08:00
updated: 2026-05-09T12:30:00+08:00
---

# MySQL 锁体系，以及乐观锁和悲观锁如何取舍？

## Variants

- MySQL 的锁有哪些？
- 乐观锁和悲观锁怎么理解、怎么选？

## Short Answer

InnoDB 锁可从粒度和模式理解：粒度有表锁、行锁（记录锁/间隙锁/临键锁）；模式有共享锁和排他锁，并配合意向锁快速判冲突。悲观锁通过先加锁保证强一致，但牺牲并发；乐观锁通过版本校验减少阻塞，适合读多写少。选择本质取决于冲突概率、一致性要求和可接受重试成本。

## Full Answer

这道题其实是两个考点合在一起了：第一个是 InnoDB 的锁机制本身，第二个是并发控制策略怎么选。面试官想看到的是你把"锁的底层行为"和"业务的取舍决策"两个层次都讲清楚。

先说锁体系。很多人一上来就背"行锁、表锁、共享锁、排他锁"，这个是基础，但真正值得展开的是 InnoDB 的行锁细分。为什么 InnoDB 要搞 Record Lock、Gap Lock、Next-Key Lock 三种行锁？这背后是一个非常经典的问题：在 RR 隔离级别下怎么解决幻读？

幻读的核心是"我读了某个范围的数据，但在我提交之前，别人在这个范围内插入了一条新数据"。单纯锁住已存在的记录不够，因为新插入的行根本还没存在。所以 InnoDB 的解法是 Gap Lock——锁住索引记录之间的间隙，不让别人插入。而 Next-Key Lock 则是 Record Lock + Gap Lock 的组合，锁住记录本身再加它前面的间隙。这是一个非常精巧的设计，但代价也很直接：Gap Lock 的范围越宽，并发度越低。如果你 where 条件没命中索引，InnoDB 会锁住整个表的间隙，这时候并发直接掉到地板。

再说意向锁（Intention Lock）。这个东西的存在理由很简单：MySQL 要判断"这张表里有没有某一行被加了排他锁"的时候，不能去遍历所有行，性能受不了。所以先在表级加一个 IX 锁做标记——有人加了行级 X 锁，表上就有 IX 锁——别的请求一看表上有 IX 锁，就知道不能加表级 X 锁了。这是一个典型的"空间换时间"设计。

然后说乐观锁和悲观锁的选择。这个面试里几乎是必问的。

悲观锁的哲学是"我假定一定有冲突，所以先锁再操作"。在 MySQL 里就是 SELECT ... FOR UPDATE。它能保证强一致性——库存扣减不会超卖，账户余额不会变负。但代价是你把并发压力全压到了数据库上。如果在高并发下事务没控制好——比如 SELECT FOR UPDATE 之后跑去调了个 RPC——锁等待会迅速堆积，整个库的 TPS 断崖式下跌。所以用悲观锁的三个铁律：事务要短、索引要走对（没索引的话 FOR UPDATE 会锁全表）、加锁顺序要固定（不然死锁分分钟）。

乐观锁的哲学是"我假定冲突很少，所以平时不加锁，提交的时候再检查"。最常见的做法就是 version 字段——UPDATE SET version=version+1 WHERE id=? AND version=?。如果 affected_rows=0，说明在你读之后别人已经改过了，这就是并发冲突信号。乐观锁的好处是不阻塞，吞吐很高，特别适合读多写少的场景。但它把复杂度转移到了应用层：你要设计重试策略（重试几次？退避多久？）、幂等保护（同一个更新请求被重试了怎么办？）、冲突反馈（让用户重新提交还是后台静默重试？）。

这里有一个面试里能让你出彩的结论：乐观锁和悲观锁不是二选一，而是分层用。热点资源（比如秒杀库存）先做分片削峰，DB 层用悲观锁兜底保证不超卖；非热点业务优先用乐观锁，吞吐好。再加一层面的是监控——锁等待时间、死锁频率、乐观锁冲突率、FOR UPDATE 的平均持锁时间——这些指标能告诉你当前策略是不是还适用。你说出这一层取舍加监控，面试官就知道你有架构视角。
## Follow-ups

- 什么时候会触发 gap lock？
- 悲观锁下如何降低死锁概率？
- 乐观锁冲突率高时该怎么改造？

## Related Concepts

- Record Lock
- Gap Lock
- Next-Key Lock
- S/X Lock
- Intention Lock
- Optimistic Lock
- Pessimistic Lock

## In Outlines

- [[interview/outlines/database]]
