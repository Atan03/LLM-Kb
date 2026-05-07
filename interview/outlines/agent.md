---
title: Agent Interview Outline
category: interview-outline
topic: agent
summary: Lecture-style revision outline for Agent architecture, control flow, tool integration, skills, and rules.
question_count: 2
chapter_count: 4
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# Agent Interview Outline

## Why This Topic Matters

Agent 平台题通常不只考“知道某个名词”，而是考你能否把模型能力、工具调用、任务控制流和工程约束组织成可落地系统。面试官会看你是否能说清楚什么时候用简单循环，什么时候需要计划器，工具能力如何标准化接入，以及能力扩展和安全约束如何分层。

## Chapter 1. Control Flow Is The Backbone

Agent 的第一层问题是控制流。ReAct 把推理和行动绑在一个短循环里，适合步骤少、反馈快、工具调用链短的任务；Plan-Execute-Replan 则先拆解目标，再分步执行，并在偏离目标时重规划，适合长任务和复杂依赖。

### Questions

- [[interview/questions/react-vs-plan-execute-replan]]

### Concepts

- ReAct
- Plan-Execute-Replan
- 任务分解
- 上下文隔离
- 模型路由

## Chapter 2. Tooling Layers

工具调用相关问题要按层回答。Function Call 是模型输出结构化参数的能力；MCP 是工具和外部资源接入的标准协议；Skill 是 Agent 框架里可复用的任务能力封装。三者不是互斥概念，而是在不同层次解决不同问题。

### Questions

- [[interview/questions/function-call-mcp-skill-rules]]

### Concepts

- Function Calling
- MCP
- Skill
- Tool Schema

## Chapter 3. Capability Versus Constraint

Skill 扩展 Agent 能做什么，Rules 限制 Agent 必须怎样做和不能做什么。生产系统里这两层必须同时存在：没有 Skill，Agent 没有业务能力；没有 Rules，Agent 容易越权、幻觉或输出不可控。

## Chapter 4. Interview Answer Pattern

回答 Agent 题时最好固定使用“场景边界 -> 架构分层 -> 工程取舍 -> 风险控制”的顺序。比如 ReAct 和计划器对比时，不只说概念差异，还要补上下文成本、死循环风险、大小模型路由和混合架构。

## Open Gaps

- 缺少实际 Agent 评测、失败恢复、长程记忆和多 Agent 协作题。
- 后续面经如果反复出现 MCP 或 Skill 设计细节，可以把工具协议单独扩成章节。
