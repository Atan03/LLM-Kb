---
title: 思维链（CoT）和思维树（ToT）有什么区别？各适合什么场景？
category: interview-question
topic: agent
subtopics: [cot, tot, reasoning, prompt-engineering, llm-application]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/llm-prompt-engineering]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 什么是思维链（CoT）和思维树（ToT）？分别适用什么场景？
summary: 考察 CoT 单链推理 vs ToT 树状搜索的适用边界，以及生产选型建议。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 思维链（CoT）和思维树（ToT）有什么区别？各适合什么场景？

## Variants

- CoT 和 ToT 的区别是什么？
- 什么场景适合用思维链，什么场景要用思维树？
- CoT 和 ToT 的工程代价是什么？

## Short Answer

CoT（Chain of Thought）在 prompt 里加"让我们一步一步思考"，把复杂问题拆成线性推理步骤，适合大多数需要推理的场景。ToT（Tree of Thought）把推理扩展成树状，每个节点生成多个候选思路再评估剪枝，适合需要多路探索和回溯的复杂决策。生产上 CoT 够用就不上 ToT。

## Full Answer

### CoT（Chain of Thought）

在 prompt 里引导模型在输出最终答案前先输出推理过程。本质是把复杂问题拆成**线性推理步骤**。

**Zero-shot CoT**：
```
问题：...
请一步一步思考，然后给出答案。
```

**Few-shot CoT**（提供推理示例）：
```
问题：xxx
思考：首先...，然后...，因此...
答案：xxx

问题：yyy（实际问题）
思考：
```

适用场景：数学计算、逻辑推理、代码分析、需要多步骤推导才能得出结论的问题。对于简单问题（直接能回答），CoT 是浪费 token。

### ToT（Tree of Thought）

把推理从单链扩展成**树状结构**。每个决策节点生成多个候选思路，用启发式函数评估每个分支的前景，选择最有希望的继续探索，遇到死路回溯。相当于给模型加了 BFS/DFS + 剪枝。

适用场景：需要探索多种可能路径的问题——创意写作、策略规划、有多种方案的工程问题。

代价：多次 LLM 调用（每个节点一次），成本和延迟都高。只在真正需要多路探索时才用。

### 对比和选型

| | CoT | ToT |
|--|-----|-----|
| 结构 | 单链 | 树状 |
| 调用次数 | 1次 | 多次 |
| 适用场景 | 大多数推理任务 | 复杂决策、多路探索 |
| 成本 | 低 | 高 |
| 典型场景 | 数学、代码分析、逻辑推理 | 策略规划、创意写作 |

生产上 **CoT 够用就不上 ToT**，成本和延迟差距很大。

## Follow-ups

- CoT 在什么情况下会失效？
- ToT 如何决定分支数量和剪枝策略？
- Self-consistency（多数投票）和 CoT 怎么结合？

## Related Concepts

- Chain of Thought
- Tree of Thought
- Prompt Engineering
- Reasoning
- Self-consistency

## In Outlines

- [[interview/outlines/agent]]
