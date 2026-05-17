---
title: 200 组固定 QA 知识库选 RAG、LoRA 微调还是长上下文输入？
category: interview-question
topic: llm-application
subtopics: [rag, lora, fine-tuning, long-context, few-shot, knowledge-base]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/llm-fine-tuning]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 对于 200 组左右的固定 QA 知识库，选择 RAG、LoRA 微调还是长上下文直接输入？
summary: 考察小规模知识库场景（200组QA）的技术选型：长上下文直接输入是 200 组规模的最优解，RAG 杀鸡用牛刀，LoRA 严重过拟合风险。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 200 组固定 QA 知识库选 RAG、LoRA 微调还是长上下文输入？

## Variants

- 200 条 QA 知识库用什么方案？
- 什么时候选 RAG 而不是直接输入？
- 样本少能做 LoRA 吗？

## Short Answer

200 组 QA 这种小规模，**长上下文直接输入**是最优解：实现最简单、零工程量、QA 更新直接改 prompt、没有检索精度损失、没有微调过拟合风险。200 条这个量级，128K+ 上下文完全放得下（不到总 token 的 2%）。

## Full Answer

### 三种方案对比

**长上下文直接输入（推荐首选）**：
- 200 条全部放进 System Prompt 或作为 context，现代大模型 128K+ 上下文完全放得下
- 实现最简单，零工程量
- QA 更新直接改 prompt，没有维护成本
- 唯一担心"lost in the middle"，但 200 条数据量小，影响可控

**RAG**：
- 200 条建向量库，工程成本不低
- 200 条检索的 recall 不可能比全量输入高——检索本身就有损失
- 杀鸡用牛刀，除非 QA 会持续增长到几千条以上，否则不划算

**LoRA 微调**：
- 200 条样本量太少，**过拟合风险极高**
- 模型会死记硬背这 200 条，换个问法就不会了
- 微调后固化了，QA 更新要重新微调，维护成本极高

### 选型决策树

```
QA 规模 ≤ 500 条？
  → 长上下文直接输入（首选）

QA 规模 500~5000 条？
  → RAG（检索 + 生成）

QA 规模 > 5000 条，或者需要把知识注入模型权重？
  → RAG + 必要时微调
```

### 小样本 LoRA 的特殊处理（仅供了解，不推荐此场景用）

如果真的要走 LoRA：
- 数据增强：GPT-4 生成改写样本，200 条扩到 1000+ 条
- 降低 rank：rank=4~8，防止过拟合
- 增大 dropout：0.1~0.3
- Early stopping：验证集 loss 上升就停

但这对于 200 条场景仍然不推荐。**少样本场景，prompt engineering + few-shot 效果比微调更稳定**。

## Follow-ups

- 什么规模应该从长上下文切换到 RAG？
- LoRA 微调适合什么场景？
- few-shot 和 RAG 怎么结合？

## Related Concepts

- RAG
- LoRA Fine-tuning
- Long Context
- Few-shot Prompting
- Knowledge Base

## In Outlines

- [[interview/outlines/llm-application]]
