---
title: RAG 如何做补充检索触发、混合检索和重排序过滤？
category: interview-question
topic: rag
subtopics: [query-rewrite, hybrid-search, bm25, rerank, threshold, hallucination]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-淘宝天猫-agent开发.md
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-快手aiagent-agent开发.md
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 补充检索是如何评估数据质量并触发的？你怎么保证二次检索能搜到之前没搜到的内容？
  - 为什么在向量检索的基础上还要加 BM25 精确检索？具体解决了什么 bad case？
  - 重排序（Rerank）是怎么做的？有没有设置低分阈值做提前过滤操作？
  - 为什么引入父子索引？
  - 为什么在检索阶段引入 BM25？
  - BM25 和向量检索是怎样组合的？比例是如何设置的？
  - 整体检索流程是怎样的？从 query 到最终上下文的完整流程是什么？
  - 检索阶段有没有做 rerank？使用的是什么方式？
  - rerank 后一般返回几个块？为什么选择这个数量？有没有做过验证？
  - rerank 后的 topK 截断是怎么做的？为什么是这个值？有没有尝试过其他策略？
  - 如果上下文长度不够或过长，你是怎么处理的？
  - 为什么在向量检索基础上还要引入 BM25 关键词检索？
summary: 考察 RAG 质量闭环：触发补检、查询重写、向量+BM25 混合检索、RRF 融合、Cross-Encoder 重排和低分阈值过滤。
created: 2026-05-09T10:43:12+08:00
updated: 2026-05-12T00:00:00+08:00
---

# RAG 如何做补充检索触发、混合检索和重排序过滤？

## Variants

- 补充检索如何触发？怎么保证能检到新内容？
- 为什么向量检索还要加 BM25？
- Rerank 如何做？是否设置低分阈值？
- 为什么引入父子索引？
- BM25 和向量检索如何融合，比例如何定？
- 从 query 到最终上下文的完整 RAG 流程是什么？
- rerank 后为什么只取 3-5 个块？
- 上下文过长或过短怎么处理？

## Short Answer

高质量 RAG 一般是“召回-评估-补检-重排-过滤”闭环。先做向量召回，再用 rerank 分数和可答性判断是否触发二次检索；二次检索通过 query rewrite、检索参数调整和 BM25 补召回覆盖盲区；最终用 cross-encoder 重排并做低分阈值截断，宁可少给上下文，也不要喂高噪声片段导致幻觉。

## Full Answer

我会把这道题讲成一条完整的 RAG 检索生产流水线，而不是“向量检索 + BM25”这几个词的拼盘。线上真实问题往往有三类：召回不到、召回不准、召回太杂。所以我会把流程拆成“query 处理 -> 多路召回 -> 融合 -> 精排 -> 父块回溯 -> 上下文预算控制”。

第一步是 query 处理。用户原始 query 往往带指代、口语化表达或者信息缺口，所以我通常会先做 query rewrite：把“它什么时候发布的”改写成带实体的完整问题；如果语料里有缩写、产品别名，也会做 query expansion。这个阶段做得不好，后面所有检索优化都是在补救脏输入。

第二步是多路召回。向量检索负责语义泛化，BM25 负责精确关键词命中。为什么要加 BM25？因为像错误码、版本号、模型名、SKU、接口名这种字符串，向量模型经常会“理解个大概”，但检不到你真正要的那条文档。BM25 则天然擅长这种词法精确命中。生产里我一般不是选边站，而是让两路各自召回 20-30 个候选。

第三步是融合与父子索引。融合层我更偏向 RRF，因为它不依赖分数量纲统一，适合把 BM25 排名和向量排名合并。如果业务对一类 query 明显偏关键词，也可以在 RRF 之前对召回数量做偏置，而不是硬写一个永远固定的 0.7/0.3。父子索引解决的是“检索粒度和生成粒度冲突”：子块小，召回准；父块大，生成有上下文。我的做法是子块召回、父块回溯、父块去重。这样既不让向量检索被长段落稀释，也避免模型拿到断章取义的碎片。

第四步是补检触发和 rerank。触发补检我不会只看一个分数，而是两类信号一起看：相关性信号（top rerank score 太低）和可答性信号（候选文档即使相关，也不足以支持完整回答）。命中之后不让模型硬答，而是进入二次检索。真正让二次检索有效的关键不是“再搜一遍”，而是改 query、扩召回、补 BM25 通道。

rerank 阶段我一般用 cross-encoder，因为它能直接看 query 和 chunk 的交互。候选集控制在 20-30 左右，延迟通常还能接受。至于 rerank 后为什么常取 3-5 个块，这不是一个“行业规定”，而是长上下文和噪声之间的拐点。块太少，回答证据不够；块太多，lost-in-the-middle 和噪声干扰都会上来。这个 K 值应该拿离线标注集看 Recall、Faithfulness、Answer Relevancy，再上线 A/B 看真实回答表现，不该拍脑袋定。

最后是上下文预算控制。过长时，我的策略顺序一般是：先丢低分块，再对长父块做段内裁剪或摘要，最后才去压 prompt 其他部分；过短时，不是盲目补块，而是判断是否真的证据不足。如果知识库里没有，就要明确返回“当前知识库没有足够信息”，这比幻觉补齐更重要。这里的工程核心是 token 预算优先级：系统提示、历史对话、检索上下文、模型输出空间，必须有清晰排序。

如果要体现生产经验，我最后一定会讲指标：Recall@k、MRR/NDCG、rerank latency、补检触发率、父块去重率、平均上下文 token、faithfulness、最终可答率。面试官通常靠这部分判断你是不是只会背 RAG 术语，还是确实调过线上系统。

## Follow-ups

- Query rewrite 怎么避免偏题？
- BM25 和向量召回如何配权？
- Rerank 阈值怎么标定？
- 为什么“少而准”的上下文更好？

## Related Concepts

- Query Rewrite
- Hybrid Search
- BM25
- RRF
- Cross-Encoder Rerank
- Score Threshold
- Hallucination Control

## In Outlines

- [[interview/outlines/rag]]
