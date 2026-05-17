---
title: LangGraph 的核心组件和优势是什么？
category: interview-question
topic: agent
subtopics: [langgraph, state-machine, workflow, checkpoint, human-in-the-loop]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - LangGraph 有哪些组件，以及它能实现的功能，这个框架的优点在哪里？
summary: 考察 LangGraph 的 State、Nodes、Edges、状态持久化、循环工作流和可控 Agent 编排能力。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-08T16:37:44+08:00
---

# LangGraph 的核心组件和优势是什么？

## Variants

- LangGraph 有哪些组件？
- LangGraph 能实现什么功能？
- LangGraph 相比普通链式 Agent 的优势在哪里？

## Short Answer

LangGraph 把 Agent 工作流建模成有状态图，核心组件是 State、Nodes 和 Edges。State 是共享状态，Nodes 是执行单元，Edges 决定下一步路由，可以是固定边或条件边。它的优势是适合长程、有状态、可循环、可中断恢复的 Agent 编排，尤其适合反思重试、人类在环、持久化、调试和复杂流程控制。

## Full Answer

LangGraph 的核心抽象是“用图来描述 Agent 工作流”。State 表示当前任务状态和共享数据；Node 是执行逻辑，可以是 LLM 调用、工具函数、检索、评估器或普通代码；Edge 决定执行流从哪个节点走向哪个节点，可以是固定路由，也可以根据状态做条件分支。

这套抽象的价值在于它比线性 chain 更接近真实 Agent。真实任务常常不是一条路走到底，而是需要循环、反思、重试、分支、人工确认和失败恢复。LangGraph 允许工作流形成循环图，让“生成 -> 检查 -> 修复 -> 再检查”成为显式结构，而不是塞在一个大 prompt 里。

另一个优势是状态管理和持久化。LangGraph 的持久化层可以把图状态保存为 checkpoint，从而支持中断恢复、人类在环、会话记忆、回放和故障恢复。面试回答时可以把它定位成低层 Agent orchestration runtime：它不替你设计业务 prompt，而是给你可控的状态机、路由、持久化和调试能力。

## Follow-ups

- LangGraph 为什么适合反思重试？
- State 里应该放什么，不应该放什么？
- 条件边和普通边怎么选择？
- Checkpointer 如何支持 human-in-the-loop？

## Related Concepts

- StateGraph
- State
- Node
- Edge
- Checkpoint
- Human-in-the-loop
- Durable Execution

## In Outlines

- [[interview/outlines/agent]]
