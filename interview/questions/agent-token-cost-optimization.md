---
title: 设计 Agent 时如何降低 Token 消耗？
category: interview-question
topic: llm-application
subtopics: [cost, context-management, tool-retrieval, caching, fine-tuning]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - 设计 Agent 的时候怎么能在保证效果的前提下减少 Token 消耗？
  - 思考一下有没有其他的方案，能够减少 Token 消耗？
summary: 考察 LLM 应用成本优化，包括输入裁剪、上下文压缩、缓存、模型层优化和输出结构瘦身。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# 设计 Agent 时如何降低 Token 消耗？

## Variants

- 设计 Agent 的时候怎么能在保证效果的前提下减少 Token 消耗？
- 除了常规方案，还有没有其他方式减少 Token 消耗？

## Short Answer

Agent 降低 Token 消耗要从输入、上下文、缓存、模型和输出五层同时做。输入侧用工具检索和意图识别，只注入相关工具；上下文侧做记忆压缩和子任务隔离；系统侧使用 Prompt Caching 和语义缓存；模型侧可以用微调或小模型路由把规则内化；输出侧精简 JSON 结构和不必要的解释。

## Full Answer

第一层是动态工具注入。生产 Agent 往往接入几十到上百个工具，如果每次都把全部 schema 放进 prompt，成本和干扰都会上升。更好的做法是先通过轻量意图识别、关键词规则或向量检索选出少量相关工具，再把这几个工具注入上下文。

第二层是上下文管理。多轮对话不能无脑拼接历史，应当把历史沉淀成实体、偏好、任务状态和关键事实摘要。复杂任务还可以采用 Plan-Execute 结构，让执行器只看当前子目标，而不是带着完整轨迹工作。

第三层是缓存。Prompt Caching 适合稳定的 system prompt、开发规范、通用工具说明等静态内容；语义缓存适合高相似度问题复用答案或执行流，例如在 Redis 上结合向量索引，对相似度极高的 query 直接返回历史结果。

第四层是模型层优化。对于高频、格式稳定、业务规则固定的场景，可以考虑微调或蒸馏，让小模型直接学会固定输出格式和业务约束，从而减少冗长提示词。也可以使用大小模型路由：复杂判断交给强模型，确定性执行交给便宜模型或传统程序。

第五层是输出结构瘦身。Function Calling 或结构化输出中，字段名、可选字段和解释文本都会消耗 token。高并发场景下可以缩短字段名、删除冗余字段，并把面向人看的解释和面向机器的结构化输出分开。

面试里最好补一句取舍：降本不能只追求少 token，还要看任务成功率、可观测性和维护成本。过度压缩上下文、过度依赖缓存或把工具 schema 裁得太激进，都可能导致调用错误和排查困难。

## Follow-ups

- Tool Retrieval 怎么设计召回和精排？
- Prompt Caching 适合缓存哪些内容？
- 语义缓存如何避免返回过期或权限不匹配的数据？
- 微调和长 system prompt 的取舍是什么？

## Related Concepts

- 动态工具注入
- 记忆压缩
- Prompt Caching
- 语义缓存
- 模型路由
- 微调

## In Outlines

- [[interview/outlines/llm-application]]
