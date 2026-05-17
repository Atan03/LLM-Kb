---
title: 如何客观评价 RAG 的效果？Faithfulness 和 Answer Relevancy 怎么理解？
category: interview-question
topic: rag
subtopics: [rag-evaluation, faithfulness, answer-relevancy, context-recall, context-precision, ragas]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/rag-evaluation]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 如何客观评价 RAG 的效果？谈谈上下文忠诚度、答案相关度的理解
summary: 考察 RAGAS 框架下的四大核心指标：Faithfulness（忠诚度）、Answer Relevancy（答案相关性）、Context Recall、Context Precision，以及生产评估集构建。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 如何客观评价 RAG 的效果？Faithfulness 和 Answer Relevancy 怎么理解？

## Variants

- RAG 怎么评估？
- Faithfulness 是什么？
- RAGAS 框架有哪些指标？
- 评估集怎么构建？

## Short Answer

RAG 评估拆成四个独立维度：Faithfulness（答案是否在上下文有依据）、Answer Relevancy（答案是否回答了问题）、Context Recall（需要的信息是否被检索到）、Context Precision（检索到的内容有多少有用）。生产重点看 Faithfulness（控幻觉）和 Context Recall（控检索质量）。

## Full Answer

### RAGAS 四大核心指标

**Faithfulness（忠诚度）**：
- 答案里每个陈述，是否都能在检索到的上下文里找到依据？
- 计算方式：把答案拆成若干陈述，逐条判断上下文里有没有支撑，有支撑的比例就是忠诚度
- 忠诚度低说明模型在"编"——这是 RAG 最核心的风险
- 越低越危险，低于 0.5 的系统上线就是给用户交付幻觉

**Answer Relevancy（答案相关性）**：
- 答案有没有回答用户的问题？
- 计算方式（巧妙）：根据答案反推几个可能的问题，和原始问题的语义相似度均值
- 相关性低说明答案跑题了，或者废话太多没有扣住问题
- 注意：答非所问和答得太泛都要扣分

**Context Recall（上下文召回率）**：
- ground truth 里需要的信息，检索到的上下文有没有覆盖到？
- 需要有标注数据（ground truth）才能算
- 召回率低说明检索没找到关键信息，系统上限被检索拖累

**Context Precision（上下文精确率）**：
- 检索到的内容里，有多少是真正有用的？
- 低说明噪声太多，检索相关性排序有问题

### 生产评估集构建

评估集不能只看构造的测试题，要覆盖**真实线上 query 分布**：

- 从生产日志里采样真实用户 query
- 覆盖不同意图类型（知识查询、任务执行、多轮对话）
- 覆盖不同难度（简单事实、推理计算、开放生成）
- 定期刷新，和线上 query drift 保持同步

### 四大指标的实际优先级

| 指标 | 重要性 | 原因 |
|------|--------|------|
| Faithfulness | ★★★★★ | 幻觉是 RAG 的根本风险 |
| Context Recall | ★★★★ | 检索不够，答案再好也没用 |
| Answer Relevancy | ★★★ | 跑题答案用户体验差 |
| Context Precision | ★★★ | 噪声多会稀释关键信息 |

## Follow-ups

- Faithfulness 低怎么优化？
- 什么情况下 Context Recall 低？
- 自动化评估和人工评估怎么结合？

## Related Concepts

- RAGAS
- Faithfulness
- Answer Relevancy
- Context Recall / Precision
- Hallucination
- RAG Evaluation

## In Outlines

- [[interview/outlines/rag]]
