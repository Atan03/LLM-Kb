---
title: Function Call、MCP、Skill 和 Rules 有什么区别？
category: interview-question
topic: agent
subtopics: [tool-use, mcp, skills, guardrails]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - Function Call、MCP、Skill 的区别？
  - Skills 和 Rules 的区别？
summary: 考察 Agent 应用栈分层：模型结构化调用能力、协议集成、技能封装和行为约束。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# Function Call、MCP、Skill 和 Rules 有什么区别？

## Variants

- Function Call、MCP、Skill 的区别？
- Skills 和 Rules 的区别？

## Short Answer

Function Call 是模型 API 层的结构化调用能力，负责把自然语言意图转成符合 schema 的参数；MCP 是模型或 Agent 与外部工具、数据源连接的标准协议；Skill 是 Agent 框架层的能力封装，通常组合 prompt、工具和执行逻辑；Rules 是约束和护栏，规定 Agent 必须做什么、不能做什么，以及安全边界。

## Full Answer

Function Call 解决的是模型如何稳定表达调用意图。它通常由模型服务提供，输入工具名、参数 schema 和用户问题，输出结构化 JSON 参数。它偏底层，是一次工具调用的表达格式。

MCP 解决的是 Agent 如何发现和连接外部能力。它把文件、数据库、浏览器、API、企业系统等资源包装成统一协议，让 Agent 不必为每个数据源写一套专用集成。可以把它理解成工具和上下文资源的标准化接入层。

Skill 解决的是业务能力如何复用。一个 Skill 通常不只是一个函数，而是一组面向任务的说明、工具、触发条件、执行流程和边界。例如“数据迁移 Skill”可以内部使用多个工具调用，也可以包含检查清单和失败处理策略。

Rules 解决的是行为约束。它不扩展 Agent 能做什么，而是约束 Agent 应该如何做、不能做什么，比如权限限制、输出格式、安全规范、禁止访问某些数据、必须确认后再执行高风险动作等。

一套清晰的面试分层可以这样说：Function Call 是模型调用工具的格式能力，MCP 是工具和资源的协议层，Skill 是业务任务的能力层，Rules 是安全和对齐的约束层。Skill 像油门，扩大可执行能力；Rules 像刹车和车道线，控制行为边界。

## Follow-ups

- Skill 内部是否一定要用 Function Call？
- MCP 和普通 HTTP API 封装有什么不同？
- Rules 应该放在 system prompt、框架配置还是运行时策略里？
- 如何避免 Skill 过多导致选择混乱？

## Related Concepts

- Function Calling
- MCP
- Agent Skill
- Guardrails
- 工具协议

## In Outlines

- [[interview/outlines/agent]]
