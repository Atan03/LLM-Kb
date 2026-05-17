---
title: 跨天长任务的 Agent 工作流如何持久化与断点恢复？
category: interview-question
topic: agent
subtopics: [long-running-workflow, checkpoint, mq, resume, state-machine]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Obsidian/LLM-Kb/interview/sources/20260509-快手-agent后端技术面.md
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260509-快手-agent开发.md
source_questions:
  - 长任务(执行几天，步骤很多)在你的项目中是怎么做的?
summary: 考察跨天长任务的状态机持久化、异步解耦、唤醒机制和失败恢复策略。
created: 2026-05-09T11:17:19+08:00
updated: 2026-05-09T12:30:00+08:00
---

# 跨天长任务的 Agent 工作流如何持久化与断点恢复？

## Variants

- 执行几天的长任务在项目中怎么做？
- Agent 长流程失败后如何恢复？

## Short Answer

长任务的关键不是“跑得久”，而是“可中断、可恢复、可追踪”。常见做法是状态机持久化 + MQ 异步执行：每个节点执行后落 checkpoint，耗时 IO 交给 worker 异步处理，完成后事件唤醒主流程；失败时从最近稳定断点恢复，而不是整链路重跑。

## Full Answer

这道题面试官真正想听的不是你能背出"用 MQ 解耦""用数据库存状态"这种八股，而是你有没有真正做过那种"一跑就是几个小时甚至几天、中间断了一次就全部白费"的任务，以及你是被折磨完之后想出来的什么方案。

我先说个真实场景。我之前做的音乐自动入库系统，一个批次要跑抓取、解析、转码、标签生成、归档，整个链路下来短则几十分钟，长了可能跨天。一开始我就用同步请求丢给 agent 跑，结果有一次跑到一半 OOM 重启了，全丢了——就是从那次之后我才认真做的持久化。

核心方案就三个字：存、拆、醒。

"存"指的是状态机持久化。我把整个长任务建模成一个显式的状态机 DAG，每个节点执行完之后立刻把 state 落盘——存在哪？小规模用 Redis，正式线上走 Postgres。重点不是说"存了"就完了，关键是你存的粒度。不能只存一个"正在执行中"，那恢复的时候什么都不知道。每个节点必须包含：输入参数、输出结果、重试次数、幂等键、时间戳。这样挂了之后从哪个节点恢复，怎么恢复，一清二楚。这里有个坑：LangGraph 的 Checkpointer 默认是同步写的，如果你每个 token 都落盘，那吞吐直接崩。生产上要做异步批量写入，或者只在节点边界写。

"拆"指的是耗时 IO 异步化。agent 主流程不直接做重 IO——下载、转码、第三方 API 调用——而是发一条消息到 RocketMQ，自己立刻进入 WAITING 状态释放线程。worker 集群消费完再通过回调事件通知 agent 继续走下一节点。这里的关键设计是 correlation_id 和幂等：回调可能重复投递，agent 收到之后要能识别"这个我已经处理过了"，直接 idempotent skip。还有超时兜底——worker 跑了一天没反应怎么办？加一个定时扫描任务表，超时的 mover 到补偿队列。

"醒"指的是断点恢复。恢复粒度必须是节点级，不是任务级。否则一个节点失败你整条链路重跑，跨天任务成本就失控了。我们线上跑的时候，同一个 agent instance 挂了重启，从最近一个成功的 checkpoint 直接 resume，毫秒级恢复。这里还有一个面试亮点的东西：加一个可观测面板。你面试的时候如果能说出来"我给每个长任务提供了一个 dashboard，能看到当前在哪个节点、已耗时多久、最近一次错误是什么、重试了几次、预计下次调度时间"，面试官立马就知道你不是纸上谈兵。

最后说一句总结：长任务的设计哲学不是"让代码跑得久"，而是"让任务经得起中断"。状态机 + checkpoint + 事件驱动 + 可观测，这四点讲全了，就是一个生产级答案。
## Follow-ups

- checkpoint 存在哪，粒度怎么选？
- 回调丢失或重复如何处理？
- 任务恢复和重试策略如何分层？

## Related Concepts

- Workflow State Machine
- Checkpoint
- MQ Callback
- Resume
- Idempotency
- Compensation

## In Outlines

- [[interview/outlines/agent]]
