---
title: 如何通过 Prompt 实现指代消除和意图识别？
category: interview-question
topic: agent
subtopics: [coreference-resolution, intent-classification, prompt-engineering, context-rewrite]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/llm-prompt-engineering]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 如何通过 Prompt 实现高效的指代消除和意图识别？
summary: 考察多轮对话中指代消除（query改写）和意图识别（结构化输出+置信度）的 prompt 设计。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 如何通过 Prompt 实现指代消除和意图识别？

## Variants

- 多轮对话里的"它"、"那个"怎么处理？
- 意图识别怎么做到结构化输出？
- 指代消除 prompt 怎么写？

## Short Answer

指代消除用 query 改写 prompt，让模型根据对话历史把最新输入改写成无歧义的独立句子。意图识别用结构化 JSON 输出（intent、confidence、slots、need_clarify），intent 类别在 prompt 里穷举，置信度低于阈值触发反问而不是猜测执行。

## Full Answer

### 指代消除（Coreference Resolution）

多轮对话里用户说"它"、"那个"、"上面说的"，直接拿去检索或执行会完全 miss。

Prompt 设计：

```
你是一个 query 改写助手。给定对话历史和用户最新输入，
将最新输入改写为一个完整的、无歧义的独立句子。
不要改变用户的意图，只补全指代和省略。

对话历史：
User: 帮我查一下 GPT-4o 的上下文长度
Assistant: GPT-4o 支持 128K token 的上下文

用户最新输入：那 Claude 3.5 呢

改写结果：
```

模型输出"Claude 3.5 的上下文长度是多少"，消除了"那"的指代，补全了省略的谓语。

关键点：改写不能改变意图，只能"补全"，否则会把用户真实意图扭曲。

### 意图识别

不要让模型输出自由文本，要**输出结构化 JSON**：

```
分析用户输入的意图，输出 JSON 格式：
{
  "intent": "query_knowledge | execute_task | chitchat | unclear",
  "confidence": 0-1,
  "slots": {
    "entity": "xxx",
    "action": "xxx"
  },
  "need_clarify": true/false

用户输入：帮我把这份报告发给张总
```

工程细节：
- intent 类别在 prompt 里**穷举**，不让模型自由发挥
- 置信度低于阈值（< 0.7）触发反问，而不是猜测执行
- Few-shot 示例要覆盖边界情况：一句话包含多个意图怎么处理

意图分类粒度不能太细（控制在 10 个以内），分类越多准确率越低。还要有 fallback 兜底，置信度低时走通用 Agent。

## Follow-ups

- 指代消除和意图识别可以合并成一个 prompt 吗？
- 置信度低时如何设计反问话术？
- 意图分类模型和 LLM 分类哪个更好？

## Related Concepts

- Coreference Resolution
- Intent Classification
- Prompt Engineering
- Structured Output
- JSON Schema

## In Outlines

- [[interview/outlines/agent]]
