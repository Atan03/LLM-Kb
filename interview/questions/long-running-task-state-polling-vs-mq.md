---
title: 长时任务状态管理：扫表和 MQ 事件驱动如何取舍？
category: interview-question
topic: mq
subtopics: [polling, event-driven, long-running-task, reliability, observability]
question_type: traditional
answer_status: reviewed
priority: high
frequency: medium
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-淘宝天猫-agent开发.md
source_questions:
  - 扫表和用消息中间件（如 Kafka 双 Topic）管理长时任务状态，各自的优缺点是啥？
  - 限流产生的积压消息，选择打回重试、落库暂存还是死信队列？各自副作用？
summary: 考察长生命周期任务的状态管理方案取舍：数据库扫表 vs MQ 事件驱动，以及积压消息三层处理策略（重试/落库/DLQ）。
created: 2026-05-09T10:43:12+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 长时任务状态管理：扫表和 MQ 事件驱动如何取舍？

## Variants

- 扫表和 Kafka 双 Topic 管理状态，各自优缺点是什么？
- 长时任务应该用 polling 还是 event-driven？

## Short Answer

扫表方案实现简单、状态可见性强、容灾直观，但实时性差且会持续压数据库；MQ 事件驱动实时性和扩展性更好，但链路复杂度、幂等、追踪和运维成本更高。长时任务通常采用混合方案：事件驱动主流程 + 数据库状态落盘 + 定时补偿扫表。

## Full Answer

长时任务我一般不建议“纯扫表”或“纯 MQ”二选一，而是按故障模型设计。纯扫表的问题是数据库被周期查询打穿，且状态变更传播慢；纯 MQ 的问题是链路长时可见性差、补偿复杂，特别是跨天任务很容易出现“消息处理过了但状态未落盘”这类一致性灰区。

更稳的模式是三件套。第一，事件驱动主流程：任务创建、阶段推进、完成回调都走消息，拿到低延迟触发和并发扩展。第二，状态中心落库：任务生命周期状态、重试次数、最后错误、幂等键必须落在 DB，便于排障和审计。第三，补偿扫描：定时任务扫描“超时未推进”“处理中卡死”“回调丢失”状态并触发修复。

我在实际里会给每个任务定义状态机（INIT/RUNNING/SUCCESS/FAILED/TIMEOUT/CANCELLED），并要求状态迁移幂等化。消费者收到消息后先做去重（task_id + stage + version），再执行阶段逻辑，最后 CAS 更新状态。这样即使消息重复、乱序也不会破坏最终一致性。

另外有两个关键点容易被忽略。第一是可观测性：要能按任务 ID 全链路追踪，从生产消息到消费处理到状态变更都可回放。第二是 SLO 设计：例如 95% 任务 5 分钟完成，超时任务 10 分钟内进入补偿。没有明确 SLO，所谓“长任务管理”很容易变成无底洞。面试时把状态机、幂等、补偿、追踪四件事讲清，基本就是成熟答案。

## Follow-ups

- 为什么长时任务不建议只靠 MQ 状态？
- 如何设计补偿任务？
- 双写场景如何保证一致性？

## Related Concepts

- Polling
- Event-driven
- Compensation
- Idempotency
- Dead Letter Queue

## In Outlines

- [[interview/outlines/mq]]
