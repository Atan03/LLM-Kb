---
title: LLM Application Interview Outline
category: interview-outline
topic: llm-application
summary: Lecture-style revision outline for LLM application cost, sampling parameters, and production trade-offs.
question_count: 2
chapter_count: 4
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# LLM Application Interview Outline

## Why This Topic Matters

LLM 应用题关注模型能力如何进入生产系统：成本怎么控，输出怎么稳定，参数怎么调，以及架构层面如何在效果、延迟、费用和可维护性之间取舍。这类问题常要求你从 prompt 之外继续往系统层、缓存层和模型层展开。

## Chapter 1. Cost Is A System Problem

Token 降本不是简单缩短 prompt。输入侧要裁剪工具和历史，上下文侧要压缩记忆和隔离子任务，系统侧要用 Prompt Caching 和语义缓存，模型侧要考虑小模型路由、微调或蒸馏，输出侧要减少冗余字段和解释。

### Questions

- [[interview/questions/agent-token-cost-optimization]]

### Concepts

- Tool Retrieval
- Memory Management
- Prompt Caching
- Semantic Cache
- Fine-tuning
- Model Routing

## Chapter 2. Sampling Parameters

Temperature 和 TopK 都作用于生成时的候选 token 分布。Temperature 控制分布平滑程度，影响稳定性和发散性；TopK 控制候选集合大小，过滤长尾低概率 token。它们能改变输出风格，但不能替代事实校验。

### Questions

- [[interview/questions/temperature-topk-decoding]]

### Concepts

- Temperature
- TopK
- Decoding
- Structured Output

## Chapter 3. Production Trade-Offs

生产里要把“省 token”和“成功率”一起看。工具裁剪过度会导致模型找不到能力，历史压缩过度会丢关键约束，语义缓存如果缺少权限和时效校验会返回错误结果。一个好的回答要主动讲风险和监控。

## Chapter 4. Interview Answer Pattern

回答 LLM 应用题时，可以按“输入 -> 上下文 -> 缓存 -> 模型 -> 输出 -> 风险”展开。这样比只列 prompt 技巧更像工程方案，也更贴近平台研发岗位。

## Open Gaps

- 缺少 RAG、评测、结构化输出稳定性、线上监控和安全治理题。
- 后续如果同类题增多，可以把成本优化独立成一个专题章节。
