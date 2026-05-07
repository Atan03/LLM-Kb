---
title: Temperature 和 TopK 如何影响大模型生成？
category: interview-question
topic: llm-application
subtopics: [decoding, sampling, model-parameters]
question_type: traditional
answer_status: reviewed
priority: medium
frequency: medium
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - 大模型调参过程中经常调两个参数，一个是温度，一个是 topK，你怎么理解这两个参数？
summary: 考察对采样参数的理解，以及不同生产任务中稳定性和创造性的取舍。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# Temperature 和 TopK 如何影响大模型生成？

## Variants

- 大模型调参过程中经常调两个参数，一个是温度，一个是 topK，你怎么理解这两个参数？

## Short Answer

Temperature 调整候选 token 概率分布的平滑程度，低温更确定，高温更发散；TopK 先截断候选集合，只保留概率最高的 K 个 token，过滤长尾低概率候选。生产中，代码、数据分析、结构化输出通常用低温度；文案、头脑风暴、角色表达可以适当提高温度。

## Full Answer

模型生成下一个 token 时，会对词表中所有 token 形成一个概率分布。TopK 是候选集合截断：只保留概率最高的前 K 个 token，再在这个集合里采样。K 越小，输出越保守；K 越大，更多长尾候选有机会被选中。

Temperature 是对概率分布形状的调整。温度低时，概率分布更尖锐，高概率 token 更容易被选中，输出更稳定；温度高时，分布被压平，中等概率 token 的机会增加，输出更有变化，但也更可能跑偏。

回答时可以强调任务适配。面向事实、代码、SQL、函数调用参数、数据分析时，应该降低温度，甚至接近 0，优先稳定和可复现。面向创意写作、营销文案、开放式 brainstorming 时，可以提高温度，让模型探索更多表达。

也要注意，TopK 和 Temperature 都不是事实性保障。它们控制的是采样行为，不会让模型获得新知识；如果问题需要准确性，还需要检索、校验、约束输出格式和评测。

## Follow-ups

- Temperature 和 TopP 有什么区别？
- 为什么温度低仍然可能出错？
- Function Calling 场景应该如何设置采样参数？

## Related Concepts

- 解码采样
- 概率分布
- 结构化输出

## In Outlines

- [[interview/outlines/llm-application]]
