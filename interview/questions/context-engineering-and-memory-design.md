---
title: 上下文工程与记忆机制应该如何设计？
category: interview-question
topic: llm-application
subtopics: [context-engineering, short-term-memory, long-term-memory, memory-update, prompt-assembly]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-快手aiagent-agent开发.md
source_questions:
  - 上下文工程是怎么设计的？上下文拼接的结构是怎样的？
  - 如何避免上下文过长导致模型性能下降？
  - 记忆机制是怎么做的？短期记忆和长期记忆是如何区分和存储的？
  - 记忆更新策略是什么？
summary: 考察上下文拼接结构、短期/长期记忆分层、记忆更新策略和长上下文质量治理。
created: 2026-05-11T12:00:00+08:00
updated: 2026-05-11T12:00:00+08:00
---

# 上下文工程与记忆机制应该如何设计？

## Variants

- 上下文工程怎么设计？
- 上下文拼接结构是什么？
- 短期记忆和长期记忆怎么做？
- 记忆更新策略是什么？

## Short Answer

上下文工程的核心是“把最有用的信息以最适合模型理解的顺序放进有限预算里”。实践上通常要区分系统规则、检索证据、对话历史、用户状态和当前任务；记忆则分短期会话记忆和长期用户记忆，两者存储方式、更新频率和召回策略都不一样。成熟系统靠分层存储、结构化摘要和优先级预算，避免上下文膨胀拖垮质量。

## Full Answer

我会把上下文工程看成一个“token 预算调度器”，而不是简单的 prompt 拼接。模型上下文不是越多越好，它更像一个有限窗口里的黄金地段：谁排在前面、谁被裁掉、谁被摘要，都会直接影响回答质量。

实际拼接时，我一般会把上下文拆成几层：最前面是 system 规则和硬约束，确保模型知道边界；然后是当前任务相关的检索证据，通常带 `doc id`、source 和结构化边界；接着才是必要的历史对话摘要和用户状态；最后是当前 query。为什么这么排？因为检索证据和当前问题的耦合度最高，历史对话更多是补语境，而不是主证据。把历史对话放得太靠前，经常会把模型注意力拉偏。
```
[System Prompt]
你是一个...，请基于以下资料回答问题。如果资料中没有相关信息，请明确说明。

[检索到的上下文]
<doc id="1" source="xxx.pdf" page="3">
...父块内容...
</doc>
<doc id="2" source="yyy.md">
...
</doc>

[历史对话]
User: ...
Assistant: ...

[当前问题]
User: 当前 query
```

记忆机制我通常分短期和长期两层。短期记忆就是当前 session 的工作上下文：最近几轮对话、当前工具调用结果、任务中间状态。这个适合存在 Redis 或会话状态存储里，更新频率高，但生命周期短。长期记忆则是跨 session 的稳定信息，比如用户偏好、项目背景、常用术语、历史结论，适合存在数据库或向量库里，并在需要时检索召回。

最难的不是“存在哪”，而是“什么值得存”。我不会把所有历史都塞进长期记忆，那会迅速污染记忆层。一个实用准则是：只有跨会话仍然有价值的信息才进长期记忆，例如“用户偏好更偏业务语言而非底层术语”值得存；“用户刚刚问了今天天气”这种一次性信息不值得。生产里如果记忆无门槛追加，很快就会出现重复、冲突和陈旧偏好。

记忆更新策略上，我更偏向“周期性蒸馏 + 冲突合并”，而不是每轮都写。比如每 N 轮或者一次 session 结束后，让模型把短期上下文提炼成结构化记忆项，再与历史记忆做去重和冲突检查。如果用户偏好发生变化，新值不能简单 append，而要覆盖旧值或降低旧值权重。否则长期记忆会越来越厚，却越来越不可信。

如果要体现实战经验，我还会补两个质量点。第一，长上下文容易出现 lost-in-the-middle，所以最相关证据要前置，低相关历史要压缩。第二，记忆层必须有回滚和审计能力：哪些记忆是模型推断出来的，哪些是用户明确说过的，最好能区分。否则模型一旦把推测当事实写进长期记忆，后面整个系统都会被带偏。

## Follow-ups

- 长期记忆的召回条件怎么定？
- 记忆冲突怎么解决？
- 为什么不是每轮都更新长期记忆？
- 上下文预算里历史、检索、输出空间怎么分配？

## Related Concepts

- Context Assembly
- Short-term Memory
- Long-term Memory
- Memory Distillation
- Token Budget
- Lost in the Middle

## In Outlines

- [[interview/outlines/llm-application]]
