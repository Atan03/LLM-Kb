---
title: 三层 Agent（Root/Main-Fallback/Sub）如何协同编排？
category: interview-question
topic: agent
subtopics: [orchestration, router, fallback, subagent, context-passing]
question_type: emerging
answer_status: reviewed
priority: high
frequency: medium
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-淘宝天猫-agent开发.md
source_questions:
  - 详细介绍一下你项目里的多智能体协同策略，三层 Agent（Root、Main/Fallback、Sub-Agent）是怎么互相配合流转的？
  - 如果主 Agent 决定越过第二层直接调底层的子 Agent，上下文信息是怎么跨层传过去的？
  - 详细描述 Multi-Agent 三层架构（Router → Manager → Sub-Agent）的设计逻辑
summary: 考察多层 Agent 编排、逐级降级、跨层上下文传递与 token 成本控制，以及 Router/Manager/Sub 三层设计逻辑。
created: 2026-05-09T10:43:12+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 三层 Agent（Root/Main-Fallback/Sub）如何协同编排？

## Variants

- 三层 Agent 如何配合流转？
- 主 Agent 跨层直调子 Agent 时怎么传上下文？

## Short Answer

可把多 Agent 设计成三层：Root 负责意图路由，Main 负责主业务决策与编排，Fallback 负责异常兜底，Sub-Agent 负责专用技能执行。跨层调用时建议“控制流传轻量指令，数据流查共享上下文”：层间传 Intent + Task ID，完整上下文落在共享存储按需拉取，减少 token 和内存压力。

## Full Answer

我会把三层架构解释成“决策分层 + 执行隔离 + 降级闭环”。Root 负责粗粒度意图路由和策略入口，Main 负责任务规划、工具编排和状态推进，Sub 只负责单技能执行（检索、SQL、API 调用等），Fallback 则是 Main 的失效保护，不是独立业务层。

生产里最重要的是边界契约。Root 输出的不是自然语言，而是结构化路由结果（intent、risk_level、required_tools）；Main 给 Sub 的不是整段对话，而是任务指令（task_id、subgoal、inputs_schema、deadline）；Sub 返回标准结果包（status、payload、confidence、error_code）。有了契约，才能做监控、重放和替换。

跨层上下文我不会层层复制，而是“控制流传指令，数据流走上下文存储”。例如 Root/Main 只传 `intent_json + task_id`，Sub 通过 task_id 去 Redis/StateStore 拉取所需字段。这样能把 10KB~50KB 的历史对话压缩成几十字节控制消息，token 成本和延迟都明显下降。并且可以做字段级授权，避免 Sub 看到不该看的上下文。

Fallback 触发也要工程化，不是拍脑袋切模型。常见触发条件是：主路径超过 deadline、关键工具连续失败、结果校验不通过、风险规则命中。触发后可以走三种动作：切更强模型、切保守流程、切人工确认。关键是每次 fallback 都打标签，回灌评估集，不然系统永远在“隐性降级”。

最后我会强调可观测性：每次 handoff 记录 trace_id、from_agent、to_agent、input_size、tool_calls、latency、error_code。面试官听到这部分通常会认可你不是在讲概念，而是讲一套可以上线运维的多 Agent 系统。

## Follow-ups

- Root、Main、Sub 的边界怎么划？
- Fallback 触发条件如何定义？
- 跨层上下文如何做权限裁剪？
- 如何防止多 Agent 踢皮球？

## Related Concepts

- Router
- Fallback
- Sub-Agent
- Intent JSON
- Shared Context
- Handoff Trace

## In Outlines

- [[interview/outlines/agent]]
