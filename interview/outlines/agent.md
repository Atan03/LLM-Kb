---
title: Agent Interview Outline
category: interview-outline
topic: agent
summary: Compact study guide for Agent architecture, AI Coding, orchestration, skills, and tooling questions.
question_count: 14
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# Agent Interview Outline

## Study Guide

Agent 面试不要只背框架名，要围绕一条平台设计主线回答：先决定任务怎么被控制，再决定能力怎么被接入和约束，最后说明如何在真实项目里验证、调试和迭代。

控制流是第一层。ReAct 是边想边做，适合短周期、步骤明确、强依赖实时反馈的任务；Plan-Execute-Replan 是先规划、再执行、必要时重规划，适合长周期、复杂依赖、容易迷失目标的任务。

把控制流落到工程取舍。ReAct 简单、反馈快，但长链路会让上下文膨胀，也容易在失败工具上循环；Plan-Execute-Replan 更容易做上下文隔离和大小模型路由，但需要额外规划成本，也要处理计划质量和重规划触发。LangGraph 这类框架的价值，是把这些流程显式建成有状态图，让循环、条件路由、checkpoint 和人类在环变得可控。

接着切到工具和能力分层。Function Call 是模型输出结构化参数的底层能力；MCP 是 Agent 接入外部工具和资源的标准协议；Skill 是框架层对一类业务能力的封装；Rules 是安全、格式、权限和行为边界。Skill 系统要有 registry、动态注入、权限过滤和审计，而不是把所有能力一次性塞进上下文。

在 AI Coding 场景里，这条主线会落到代码库级上下文一致性：先规划需求，再用 repo RAG、AST、调用图和依赖图找准上下文，随后小步生成、运行测试、根据报错自修复。DeepAgents 和 Swarm 类模式则进一步把长任务拆成 supervisor、subagents、filesystem/memory 和 handoff，让上下文隔离和专业分工更明确。

当系统进入多 Agent 协同时，常见落地结构是 Root/Main-Fallback/Sub 三层：Root 做意图路由，Main 做业务决策与编排，Fallback 处理异常降级，Sub 专注技能执行。跨层调用时推荐“控制流传 Intent+TaskID，数据流查共享上下文”，既保留必要背景又避免 token 膨胀。

对跨天长任务，Agent 设计还要补“可中断恢复”能力：节点级 checkpoint、异步 worker、事件唤醒、失败补偿和断点 resume。否则流程一旦中断只能从头重跑，工程成本不可接受。Claude Code Hooks 这类机制则提供了运行时确定性控制，把安全、规范和自动化测试前置到工具调用生命周期中。

再往下一层，就是工具治理与任务规划。Function Calling 不是简单把 schema 暴露给模型，而是要把工具边界、参数约束、规划 DAG、并行/串行依赖、失败重试与熔断全都纳入编排设计。一个成熟 Agent 系统必须既能规划，也能在工具失败时优雅收敛，而不是无限循环。

## Interview Thread

建议按这个顺序复习：

- [[interview/questions/react-vs-plan-execute-replan]]：先建立 Agent 控制流的判断标准。
- [[interview/questions/langgraph-components-and-advantages]]：理解状态图如何把 Agent 流程显式化。
- [[interview/questions/deepagents-and-agent-swarm-architecture]]：把长程任务、多 Agent 协作和上下文隔离串起来。
- [[interview/questions/function-call-mcp-skill-rules]]：建立 Agent 能力层和约束层的分层表达。
- [[interview/questions/skill-system-design-general-and-specific]]：把 Skill 抽象落到 registry、动态注入和权限治理。
- [[interview/questions/ai-coding-fullstack-context-consistency]]：把 Agent 设计落到 AI Coding 的真实项目闭环。
- [[interview/questions/multi-agent-three-layer-orchestration]]：把三层协同、fallback 和跨层上下文传递讲清楚。
- [[interview/questions/long-running-agent-workflow-persistence-and-resume]]：把跨天长任务的持久化与断点恢复说清楚。
- [[interview/questions/claude-code-hooks-engineering-controls]]：把 runtime Hook 的质量门禁和安全治理说清楚。
- [[interview/questions/function-calling-planning-and-tool-orchestration]]：把工具 schema、规划混合策略、调用顺序和失败恢复讲清楚。

这条复习线可以支撑“如何设计一个 Agent 平台”的初版系统回答。

## Questions

- [[interview/questions/react-vs-plan-execute-replan]]
- [[interview/questions/function-call-mcp-skill-rules]]
- [[interview/questions/mcp-agent-implementation]]
- [[interview/questions/langgraph-components-and-advantages]]
- [[interview/questions/deepagents-and-agent-swarm-architecture]]
- [[interview/questions/skill-system-design-general-and-specific]]
- [[interview/questions/ai-coding-fullstack-context-consistency]]
- [[interview/questions/multi-agent-three-layer-orchestration]]
- [[interview/questions/long-running-agent-workflow-persistence-and-resume]]
- [[interview/questions/claude-code-hooks-engineering-controls]]
- [[interview/questions/function-calling-planning-and-tool-orchestration]]
- [[interview/questions/cot-vs-tot-reasoning-patterns]]
- [[interview/questions/agent-coreference-resolution-intent-recognition]]

## Key Concepts

- ReAct
- Plan-Execute-Replan
- Context Isolation
- Model Routing
- Function Calling
- MCP Server / MCP Client
- MCP SDK / Tool Schema
- Skill
- Rules
- Guardrails
- LangGraph
- DeepAgents
- Subagents
- Skill Registry
- Repo RAG
- Self-Repair
- Root/Main/Sub Orchestration
- Intent JSON
- Shared Context
- Checkpoint Resume
- Hook Middleware
- Planning DAG
- Tool Failure Recovery
- Trace / LangFuse
- CoT / ToT
- Coreference Resolution
- Intent Classification
- Structured Output

## Open Gaps

- 还缺少 Agent 安全、线上观测、权限策略、长期记忆质量和多 Agent 失败恢复的更多面经样本。
