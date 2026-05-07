---
title: ReAct 和 Plan-Execute-Replan 如何选择？
category: interview-question
topic: agent
subtopics: [planning, tool-use, control-flow, cost]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - 讲一下 ReAct 和 Plan-Execute-Replan 的使用场景
  - 讲一下 ReAct 和 Plan-Execute-Replan 的区别
summary: 考察 Agent 控制流选择、任务复杂度判断、上下文隔离和大小模型解耦。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# ReAct 和 Plan-Execute-Replan 如何选择？

## Variants

- 讲一下 ReAct 和 Plan-Execute-Replan 的使用场景。
- 讲一下 ReAct 和 Plan-Execute-Replan 的区别。

## Short Answer

ReAct 适合短周期、步骤少、强依赖实时工具反馈的任务；Plan-Execute-Replan 适合长周期、目标复杂、容易在局部步骤里迷失的任务。核心区别在控制流：ReAct 是边想边做的局部决策，Plan-Execute-Replan 先形成全局计划，再按子任务执行，并在必要时重规划。后者更容易做上下文隔离和大小模型路由，但前期规划成本更高。

## Full Answer

ReAct 的优势是反馈链路短。模型每轮根据当前观察决定下一步行动，因此适合天气查询、简单检索、轻量数据库查询、单轮工具调用链等任务。它的坏处是每一步思考、工具返回和历史轨迹都会进入后续上下文，循环一长就会膨胀；如果工具报错或目标不清，也容易反复尝试同一种失败动作。

Plan-Execute-Replan 的优势是先把目标拆成显式任务队列或任务图。Planner 负责站在全局视角拆解目标，Executor 只处理当前子任务，Replanner 在执行结果偏离预期时修正计划。这种结构适合长文档分析、多文件代码修改、复杂数据处理、报告生成等任务，因为它能保持总目标、进度和依赖关系。

从工程角度看，Plan-Execute-Replan 还有两个重要收益。第一是模型解耦：可以用能力更强、成本更高的模型做规划，用便宜快速的模型或确定性工具执行子任务。第二是上下文隔离：Executor 不需要携带完整历史，只需要当前子目标、必要输入和局部结果。相比之下，ReAct 的思考和行动绑得更紧，长链路成本和不稳定性更明显。

面试回答时可以用一句边界判断收束：任务短、反馈实时、失败成本低，优先 ReAct；任务长、依赖多、需要全局进度控制，优先 Plan-Execute-Replan；生产系统里也常混用，外层用计划器分解任务，内层对子任务执行用 ReAct 式工具循环。

## Follow-ups

- Plan-Execute-Replan 的规划失败怎么办？
- ReAct 为什么容易上下文膨胀？
- 如何在一个 Agent 系统里混用 ReAct 和 Planner？
- Planner 和 Executor 是否应该使用同一个模型？

## Related Concepts

- Agent 控制流
- 工具调用循环
- 任务分解
- 上下文隔离
- 模型路由

## In Outlines

- [[interview/outlines/agent]]
