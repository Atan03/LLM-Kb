---
title: DeepAgents 和 Agent Swarm 的设计框架是什么？
category: interview-question
topic: agent
subtopics: [deepagents, subagents, swarm, multi-agent, context-management]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - 请给我介绍一下 DeepAgents 的设计的框架，也可以在白板上面画一下他的大概架构图。那最近的 Agent Swarm 有了解么，是什么呢？
summary: 考察 DeepAgents 的 planning、filesystem、subagents、memory，以及 Agent Swarm 的并行协作与编排思想。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-08T16:37:44+08:00
---

# DeepAgents 和 Agent Swarm 的设计框架是什么？

## Freshness

- Current as of: 2026-05-08
- Sources checked:
  - LangChain DeepAgents overview: https://docs.langchain.com/oss/python/deepagents/overview
  - LangChain DeepAgents subagents: https://docs.langchain.com/oss/python/deepagents/subagents
  - OpenAI Swarm repository / handoff pattern references: https://github.com/openai/swarm
  - Kimi K2.5 tech blog (Agent Swarm): https://www.kimi.com/blog/kimi-k2-5
  - Kimi Agent Swarm blog: https://www.kimi.com/blog/agent-swarm.html
  - Kimi K2.6 Agent Swarm help doc: https://www.kimi.com/help/agent/agent-swarm

## Variants

- DeepAgents 的设计框架是什么？
- 如何画 DeepAgents 的大概架构？
- 最近的 Agent Swarm 是什么？

## Short Answer

DeepAgents 可以理解成构建长程 Agent 的 harness：在基础 tool-calling loop 上加 planning、文件系统式上下文管理、subagents、长程记忆和 LangGraph runtime。白板上可以画成 Supervisor Agent 居中，下面接 tools、todo/plan、filesystem/memory、subagents，底层由 LangGraph 提供 durable execution、streaming、human-in-the-loop 和 persistence。Agent Swarm 则是多 Agent 并行协作范式：由 orchestrator 按任务动态创建和调度子 agent，通过 handoff、共享状态和工具路由并发执行。

## Full Answer

DeepAgents 的设计重点是让 Agent 能处理复杂、长周期、多步骤任务，而不是只做一次工具调用。它通常包含几层：第一层是主 Agent 或 supervisor，负责理解目标、规划和协调；第二层是 planning/todo 机制，把复杂目标拆成可跟踪步骤；第三层是 filesystem 或 memory，把长上下文、中间产物和长期知识从模型上下文里外置出来；第四层是 subagents，把研究、编码、分析、验证等子任务隔离到专门 Agent 中；第五层是底层运行时，负责持久化、流式执行、中断恢复和人类在环。

这个架构的好处是解决三个常见问题。第一是长任务没有计划会漂移，所以需要 todo/plan。第二是上下文窗口会被工具输出撑爆，所以需要文件系统和摘要。第三是所有工作都塞给一个 Agent 会混乱，所以要用 subagents 做上下文隔离和专业化分工。

Agent Swarm 更偏“并行组织”思想，而不仅是固定分工。它可以由 orchestrator 根据任务实时创建、分配和回收子 agent，而不是预先写死角色。和传统多 Agent 编排相比，关键差异是调度粒度更细、并发规模更高、工具调用密度更大、对状态汇聚和冲突消解要求更强。

结合 2026 年的公开资料，Kimi K2.5 把 Agent Swarm 明确提出为“self-directed swarm”范式：模型可自导向创建最多 100 个并行 sub-agents，并在一次任务中执行高密度工具调用（公开材料提到最多约 1500 次），目标是把串行长任务改为并行执行以缩短端到端时延。Kimi K2.6 在产品文档中把这个上限继续扩到更大规模并行（帮助文档写到最多 300 个 sub-agents）。这说明 Swarm 在行业里的重点正在从“多角色协作”转向“自动编队 + 并行扩展 + 调度优化”。

面试里可以这样收束：Swarm 不是一个固定框架名，而是一类架构能力。稳定思想是 orchestrator、dynamic subagents、parallel execution、handoff protocol、shared state/memory、tool permission 和可观测性；具体实现会随模型和运行时演进。

## Follow-ups

- DeepAgents 和 LangGraph 是什么关系？
- Subagents 什么时候值得用，什么时候不值得？
- 多 Agent Swarm 如何避免互相踢皮球？
- Swarm 中状态共享和权限控制怎么做？

## Related Concepts

- DeepAgents
- Supervisor Agent
- Subagents
- Context Isolation
- Handoff
- Swarm
- Filesystem Memory
- Durable Execution

## In Outlines

- [[interview/outlines/agent]]
