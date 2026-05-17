---
title: Agent 记忆系统如何设计？
category: interview-question
topic: memory-and-context
subtopics: [memory-architecture, structured-memory, conflict-resolution, context-structure]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-美团-agent开发.md
source_questions:
  - 用一个例子解释一下你的 Memory 是怎么做的，以及怎么插入上下文的？
  - 结构化信息和文本信息之间如何区分？会不会重复？
  - 记忆冲突怎么解决？比如用户前后说了不同的过敏信息？
  - 记忆是怎么写入上下文的？上下文从头到尾结构是怎样的？
  - 上下文结构是不是：System Prompt + Memory + Tools Result + Conversation 这四个环节？
summary: 考察 Agent 记忆系统的整体架构设计，包括结构化和文本记忆的区分、冲突解决、上下文组装结构。
created: 2026-05-11
updated: 2026-05-11
---

# Agent 记忆系统如何设计？

## Variants

- 你的记忆系统是怎么做的？怎么插入上下文？
- 结构化信息和文本信息之间如何区分？会不会重复？
- 记忆冲突怎么解决？
- 上下文从头到位的结构是怎样的？
- System Prompt + Memory + Tools + Conversation 的结构对不对？

## Short Answer

记忆系统的核心不是"存了什么"，而是"什么时候存、怎么存、什么时候取、取到怎么用"。好的设计分三块：记忆蒸馏（从对话中提取有价值信息）、记忆存储（结构化+文本双通道）、记忆召回（分层检索+混合注入）。上下文组装顺序是 System Prompt → Memory → Tool Definitions → Conversation History → Current Query，这个顺序由模型对首尾内容关注度更高这个特性决定。

## Full Answer

这道题面试官真正想听的不是你背出"我用了向量数据库存记忆"这种话，而是你有没有真的处理过记忆系统里那些真实又琐碎的坑。

先说核心观点：记忆系统不是数据库问题，是**信息生命周期管理**问题。一条信息从用户嘴里说出来，到最后被模型引用，中间经过：提取→分类→存储→冲突检测→召回→注入→使用，每个环节都有坑。

我用一个外卖助手的场景来讲。用户说"我对花生过敏，帮我推荐一份午餐"。这句话里有两类东西：偏好约束类（花生过敏）应该长期保存，请求类（推荐午餐）是一次性的。对话结束后，后端做一次**记忆蒸馏**——让 LLM 判断这轮对话里哪些是值得保留的，把"花生过敏"提炼成结构化 entry。下次用户说"帮我点一份晚餐"，系统先**向量化 query**去 memory store 检索，召回"花生过敏"，注入到 prompt 里，模型就会自动排除含花生的食品。

这里的关键设计是**存储双通道**：结构化 vs 文本。能结构化的（过敏、地址、预算）走精确字段，方便 upsert 和冲突检测；难以结构化的（"上次那家店服务很好"）走文本+向量检索。两类之间可能有重叠，处理原则是**结构化优先**——如果能提取成字段就不留文本版本。

记忆冲突是最容易踩的坑。假设用户第一次说"对花生过敏"，第二次又说"花生没问题，是对芝麻过敏"。处理策略三层：时间优先（默认最新的为准），置信度加权（随口提一次的置信度低不能覆盖多次确认的），显式确认（过敏这类安全问题不自动覆盖，发消息让用户确认）。安全敏感信息宁可多问一次，不能静默覆盖。

上下文组装顺序也很讲究。从前往后是：System Prompt（角色+约束+格式）→ Long-term Memory（结构化+文本 topK）→ Tool Definitions（schema）→ Conversation History（最近 N 轮）→ Current Query。这个顺序利用了模型对上下文头尾关注度更高的特性——最重要的约束放最前，当前问题放最后。注意 Tools Result 不是独立一块，而是嵌在 Conversation History 里的 role:tool 消息对。

## Follow-ups

- 结构化记忆和文本记忆的存储分别用什么技术选型？
- 记忆蒸馏的触发时机和频率怎么设计？
- 多用户多 Session 下记忆隔离怎么做？

## Related Concepts

- Structured Memory
- Memory Distillation
- Conflict Resolution
- Context Assembly
- Vector Retrieval

## In Outlines

- [[interview/outlines/memory-and-context]]
