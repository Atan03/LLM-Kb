---
title: Memory and Context Interview Outline
category: interview-outline
topic: memory-and-context
summary: Compact study guide for Agent memory system design, including memory architecture, structured vs text storage, conflict resolution, context assembly, and retrieval strategies.
question_count: 1
chapter_count: 0
status: growing
created: 2026-05-11
updated: 2026-05-11
---

# Memory and Context Interview Outline

## Study Guide

Memory 系统的核心不是"存了什么"，而是信息生命周期管理：提取→分类→存储→冲突检测→召回→注入→使用。每步都有坑。

设计通常分三个维度。存储维度：结构化记忆（过敏、地址、预算）走精确字段 upsert，文本记忆（偏好、评价）走向量检索，两路混合召回。检索维度：Profile 层全量注入，Episode 层向量召回的，Knowledge 层结构化+标签索引。时间维度：Session 内走 Conversation History，跨 Session 短期走热存储，长期走冷存储+时间衰减。

冲突解决是容易被忽略但生产必遇的题：时间优先（默认最新的）、置信度加权（随口提的不能覆盖多次确认的）、显式确认（安全敏感信息宁可多问一次）。

上下文组装顺序：System Prompt → Memory Block → Tool Definitions → Conversation History → Current Query。这个顺序利用了模型对首尾内容关注度更高的特性。

## Interview Thread

从 `[[interview/questions/memory-system-design]]` 开始建立记忆系统的整体架构认知，把存储双通道、冲突解决、上下文组装串起来。后续补充记忆检索的具体设计（分层检索、向量漂移、冷启动等）——这些细节在 [[interview/questions/memory-retrieval-rag]] 里。

## Questions

- [[interview/questions/memory-system-design]]

## Key Concepts

- Memory Distillation
- Structured vs Text Memory
- Conflict Resolution
- Context Assembly Order
- Layered Retrieval
- Vector Drift
- Cold Start
- Memory Age Decay

## Open Gaps

- 缺少跨 Session 记忆隔离、记忆数据量膨胀后的存储性能优化、多模态记忆的面经样本。
