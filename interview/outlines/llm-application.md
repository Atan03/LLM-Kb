---
title: LLM Application Interview Outline
category: interview-outline
topic: llm-application
summary: Compact study guide for LLM application cost, context compression, and decoding questions.
question_count: 4
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-11T15:07:11+08:00
---

# LLM Application Interview Outline

## Study Guide

LLM 应用面试可以围绕生产化主线回答：系统既要便宜可控，也要输出稳定、适配任务。Token 成本、上下文管理、缓存、模型选择和生成参数，本质上都在服务这个目标。

Token 降本不要停在“缩短 prompt”。一个更像工程方案的回答，应该从输入、上下文、缓存、模型和输出五层展开。上下文压缩是这里的高频追问：什么时候压、压成什么、压完怎么验，直接决定了长任务成功率。

输入侧，用意图识别或工具检索，只注入相关工具 schema；上下文侧，把长历史压缩成实体、偏好、任务状态和摘要，复杂任务还可以用子任务隔离；缓存侧，静态 system prompt 和工具说明可以用 Prompt Caching，高相似 query 可以用语义缓存；模型侧，高频稳定场景可以考虑微调、小模型路由或蒸馏；输出侧，结构化字段和解释文本都可以瘦身。

采样参数题则回答“如何让输出适合任务”。Temperature 控制概率分布的平滑程度，低温更稳定，高温更发散；TopK 控制候选集合大小，过滤低概率长尾 token。代码、数据分析、函数调用参数这类任务要偏低温；文案、发散创意可以适当提高随机性。

另一个经常被追问的点是上下文工程与记忆。真正上线的系统不会把所有历史无脑拼进 prompt，而是要区分系统规则、检索证据、会话状态、短期记忆和长期记忆，并按 token 预算做优先级分配。记忆更新也不是每轮 append，而是周期性蒸馏、冲突合并和价值筛选。

沿着生产化视角看，LLM 应用的核心是：用系统设计控制成本，用解码参数控制生成行为，用上下文工程保障长期稳定性，但三者都不能替代事实校验、权限校验和效果评测。

## Interview Thread

建议按这个顺序复习：

- [[interview/questions/agent-token-cost-optimization]]：先建立 LLM 应用成本优化的系统框架。
- [[interview/questions/context-compression-trigger-strategy]]：补齐上下文压缩触发策略与质量护栏。
- [[interview/questions/context-engineering-and-memory-design]]：补齐上下文拼接、短期/长期记忆和记忆更新策略。
- [[interview/questions/temperature-topk-decoding]]：再理解生成参数如何影响稳定性和创造性。

这条复习线能支撑“如何把 LLM 能力稳定、便宜地放进生产系统”的初版回答。

## Questions

- [[interview/questions/agent-token-cost-optimization]]
- [[interview/questions/context-compression-trigger-strategy]]
- [[interview/questions/context-engineering-and-memory-design]]
- [[interview/questions/temperature-topk-decoding]]

## Key Concepts

- Tool Retrieval
- Memory Compression
- Context Compression Trigger
- Short-term vs Long-term Memory
- Prompt Caching
- Semantic Cache
- Fine-tuning
- Model Routing
- Temperature
- TopK
- Decoding

## Open Gaps

- 还缺少 RAG、结构化输出、线上评测、失败分析、监控、安全和多模型路由策略题。
