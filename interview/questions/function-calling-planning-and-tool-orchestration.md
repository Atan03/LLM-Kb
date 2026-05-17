---
title: Function Calling、任务规划和多工具调度应该如何设计？
category: interview-question
topic: agent
subtopics: [function-calling, planning, tool-ordering, tool-failure, orchestration]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-快手aiagent-agent开发.md
source_questions:
  - Function Calling 是怎么设计的？
  - Agent 的任务规划是怎么做的？规划是由模型完成还是通过规则实现？
  - 多工具调用时如何决定调用顺序？
  - 如果工具调用失败如何处理？
summary: 考察 Function Calling 设计、模型与规则混合规划、多工具顺序决策和失败处理策略。
created: 2026-05-11T12:00:00+08:00
updated: 2026-05-11T12:00:00+08:00
---

# Function Calling、任务规划和多工具调度应该如何设计？

## Variants

- Function Calling 是怎么设计的？
- 任务规划是模型做还是规则做？
- 多工具调用顺序怎么定？
- 工具调用失败怎么处理？

## Short Answer

Function Calling 的核心不是 schema 能跑通，而是让模型“选得准、填得对、失败可恢复”。成熟系统通常是混合式：高层流程由规则约束边界，具体步骤由模型规划；工具顺序按依赖关系和并行性决定；失败处理则分瞬时重试、替代降级、显式回传和熔断止损四层。

## Full Answer

我会把这题拆成四个工程问题：工具怎么定义、任务怎么规划、顺序怎么排、失败怎么收。

先说 Function Calling。很多系统的失败不是模型不会调用，而是工具定义太含糊。一个好工具定义至少要把三件事写清：什么时候用、输入约束是什么、什么情况下不要用。尤其 `description` 不能只写“查询天气”，而要写“用于获取指定城市未来 7 天天气，不适合回答历史天气或空气质量问题”。模型选工具主要靠这个语义边界。

再说任务规划。我很少用“全模型规划”或“全规则规划”这种极端方案。纯规则稳定，但开放任务一来就僵；纯模型灵活，但路径漂移、debug 困难。生产上更常见的是混合模式：高层阶段规则化，例如“检索 -> 分析 -> 生成 -> 校验”，阶段内再让模型决定具体工具与参数。这样既可控，也保留适应性。

工具顺序本质是依赖图问题。如果 B 依赖 A 输出，那就串行；如果多个工具互相独立，就并行 fanout，最后聚合结果。我会要求规划结果里显式标出 dependency 和 parallelizable 标记，执行层再按 DAG 跑。这样顺序不是“模型随便想”，而是可验证、可回放的执行计划。

失败处理我一般分四层。第一层是瞬时重试：超时、429、网络闪断做指数退避。第二层是降级替代：主工具失败时能否换备选工具或缓存结果。第三层是显式回传：把错误 observation 返回给模型，让它重规划，而不是把失败吞掉继续幻觉输出。第四层是熔断止损：连续失败就终止该工具，返回明确错误或触发人工接管，避免死循环烧 token。

这题最能体现经验的地方，是你有没有把工具当成“线上依赖”来治理。线上我会看工具选择准确率、参数填充错误率、单工具失败率、重试成功率、fallback 触发率、计划偏移率。如果你能把 Function Calling 从“模型 feature”讲成“依赖治理系统”，面试官一般会很买账。

## Follow-ups

- 工具 description 应该写多细？
- 什么时候适合规则规划，什么时候适合模型规划？
- 并行工具调用如何做超时和聚合？
- 工具失败后如何防止 agent 无限重试？

## Related Concepts

- Function Calling
- Tool Schema
- Planning DAG
- Parallel Tool Execution
- Retry and Fallback
- Circuit Breaker

## In Outlines

- [[interview/outlines/agent]]
