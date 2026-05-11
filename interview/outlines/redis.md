---
title: Redis Interview Outline
category: interview-outline
topic: redis
summary: Compact study guide for current Redis big-key interview questions.
question_count: 1
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T10:27:14+08:00
---

# Redis Interview Outline

## Study Guide

Redis 大 key 是典型线上治理题：不是只问“怎么拆”，而是问你能否从性能风险、访问层改造、老数据兼容、灰度迁移和最终清理，把一件缓存改造讲成完整工程方案。

Hash 大 key 的第一步不是立刻给方案，而是先说清危害：field 太多或 value 太大，会让 Redis 单线程命令执行变慢，也会放大网络传输、主从同步、持久化和集群迁移的压力。

优化主线是拆分。按照 field 或业务 id 做 hash 取模，把一个大 Hash 拆成多个小 Hash，例如 `key:000` 到 `key:999`。这里要补两个工程点：分桶算法要稳定，访问逻辑要收敛在统一封装里，否则业务代码到处拼 key，后续很难再迁移。

迁移主线是平滑。不能直接删老 key，也不能只写新 key。稳妥做法是双写、优先读新、未命中降级读老并回填、后台限速扫描迁移、灰度放量、观察新老 key 命中率，最后确认老业务下线后再删老缓存。

## Interview Thread

从 `[[interview/questions/redis-hash-big-key-sharding-migration]]` 开始，回答时按“危害 -> 拆分 -> 兼容迁移 -> 监控清理”走。这个顺序能体现你不是只会数据结构优化，而是知道生产系统要可回滚、可灰度、可观测。

## Questions

- [[interview/questions/redis-hash-big-key-sharding-migration]]

## Key Concepts

- Big Key
- Hash Sharding
- Bucket
- Dual Write
- Fallback Read
- Backfill
- Gray Release

## Open Gaps

- 还缺少热 key、缓存穿透/击穿/雪崩、分布式锁、Redis 持久化、集群槽迁移和缓存一致性题。
