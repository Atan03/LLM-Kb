---
title: Redis Interview Outline
category: interview-outline
topic: redis
summary: Lecture-style revision outline for Redis big key optimization and cache migration.
question_count: 1
chapter_count: 4
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# Redis Interview Outline

## Why This Topic Matters

Redis 面试题常从一个线上问题切入，考你是否理解单线程模型、数据结构规模、迁移兼容和灰度发布。Hash 大 key 是典型生产题，因为它同时牵涉性能、集群同步、历史数据和业务兼容。

## Chapter 1. Diagnose The Big Key

Hash 大 key 的核心风险是单个 key 的 field 数过多、value 过大或访问过重，可能造成命令阻塞、网络抖动、主从同步压力、迁移慢和持久化压力。回答时先讲风险，再讲治理。

## Chapter 2. Split By Stable Sharding

常见方案是对 field 或业务 id 做 hash 取模，把一个大 Hash 拆成多个小 Hash，例如 `key:000` 到 `key:999`。关键点是分桶算法要稳定，桶数要结合规模和扩展预期，读写路径要封装在统一访问层。

### Questions

- [[interview/questions/redis-hash-big-key-sharding-migration]]

### Concepts

- Big Key
- Hash Sharding
- Bucket
- Access Layer

## Chapter 3. Migrate Without Breaking Business

迁移不能直接删老 key。稳妥流程是双写、读新降级读老、未命中回填、后台限速迁移、灰度切换、监控命中率，最后确认老业务下线和老 key 命中归零后再删除。

## Chapter 4. Interview Answer Pattern

Redis 生产题要显得“稳”：先说明问题危害，再给拆分方案，然后补兼容迁移、监控指标、回滚策略和清理条件。

## Open Gaps

- 缺少热 key、缓存穿透/击穿/雪崩、分布式锁、持久化和集群槽迁移题。
