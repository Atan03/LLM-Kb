---
title: Redis Hash 大 key 如何拆分并平滑迁移？
category: interview-question
topic: redis
subtopics: [big-key, sharding, cache-migration, compatibility]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - Redis 的 Hash 大 key 怎么优化？
  - Redis 的 Hash 大 key 优化后怎么兼容老业务老数据？新业务怎么用优化后的缓存？老缓存删不删？
summary: 考察 Redis 大 key 拆分、读写兼容、异步迁移、灰度切换和缓存清理策略。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# Redis Hash 大 key 如何拆分并平滑迁移？

## Variants

- Redis 的 Hash 大 key 怎么优化？
- Hash 大 key 优化后怎么兼容老业务和老数据？
- 新业务怎么用优化后的缓存？老缓存删不删？

## Short Answer

Hash 大 key 的核心优化是拆分。可以按 field 做 hash 取模，把一个大 Hash 拆成多个小 Hash，例如 `key:{bucket}`。迁移时不能一刀切，应采用双写、优先读新、未命中读老并回填、后台异步迁移、灰度切换和监控确认。老 key 只有在老业务下线、迁移完成、命中率归零后才删除。

## Full Answer

Redis 大 key 的问题不只是占空间，还会造成主线程阻塞、网络传输抖动、集群迁移慢、持久化和主从同步压力变大。对于 Hash 大 key，如果 field 数量极大，可以按 field 名称或业务 id 做 hash，再对固定桶数取模，把一个 Hash 拆成多个小 Hash。

例如原来所有用户属性都在 `profile_hash` 里，现在可以变成 `profile_hash:000` 到 `profile_hash:999`。读写时先根据 field 计算桶号，再访问对应小 key。桶数需要结合数据规模、增长速度、Redis 实例分布和运维成本选择，并保持算法稳定。

兼容迁移一般分阶段做。第一阶段改写逻辑，新写入同时写老 key 和新分片 key，保证老业务还能读。第二阶段改读逻辑，新业务优先读新 key；如果未命中，则降级读老 key，并把结果回填到新 key。第三阶段用后台任务在低峰期扫描老 Hash，分批迁移到新 key，控制速率，避免阻塞 Redis。第四阶段灰度放量并观察新 key 命中率、老 key 命中率、错误率和延迟。

老 key 不应该立刻删除。只有当老业务已经停止依赖、迁移任务完成、新 key 命中率稳定、老 key 命中率长期为 0，并且有回滚预案时，才下线双写和降级读，最后清理老缓存。

## Follow-ups

- Hash 大 key 拆分后如何选择桶数量？
- 迁移脚本如何避免阻塞 Redis？
- 如果双写失败导致新旧不一致怎么办？
- 大 key 除了拆分还有哪些治理手段？

## Related Concepts

- Redis 大 key
- 缓存迁移
- 双写
- 灰度切换
- 回填

## In Outlines

- [[interview/outlines/redis]]
