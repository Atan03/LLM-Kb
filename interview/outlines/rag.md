---
title: RAG Interview Outline
category: interview-outline
topic: rag
summary: Compact study guide for RAG quality loops, hybrid retrieval, rerank filtering, and anti-poisoning strategies.
question_count: 5
chapter_count: 0
status: growing
created: 2026-05-09T10:52:46+08:00
updated: 2026-05-12T00:00:00+08:00
---

# RAG Interview Outline

## Study Guide

RAG 题的重点是“检索质量闭环”，不是单讲 embedding。一个完整答题链路通常是：初次召回 -> 相关性/可答性评估 -> 触发补检 -> 查询重写与混合检索 -> 重排序与阈值过滤 -> 证据化回答。

第二条主线是抗污染能力。生产环境里检索质量不仅受召回算法影响，也受数据源质量影响。来源分级、内容过滤、多源交叉验证和不确定性标注是降低投毒风险的关键。

## Interview Thread

先复习 `[[interview/questions/rag-retrieval-quality-trigger-hybrid-rerank]]`，把补检触发、BM25 混合、RRF 融合、Cross-Encoder 与阈值过滤串成闭环；再复习 `[[interview/questions/rag-anti-poisoning-and-source-trust]]`，补齐来源可信度和多源验证策略。新补充 `[[interview/questions/memory-retrieval-rag]]`，把记忆检索放入 RAG 大框架：向量检索 vs 精确匹配、分层检索设计、向量漂移和冷启动等真实工程坑。

## Questions

- [[interview/questions/rag-retrieval-quality-trigger-hybrid-rerank]]
- [[interview/questions/rag-anti-poisoning-and-source-trust]]
- [[interview/questions/memory-retrieval-rag]]
- [[interview/questions/rag-chunking-strategy]]
- [[interview/questions/rag-evaluation-metrics]]

## Key Concepts

- Query Rewrite
- Hybrid Search
- BM25
- RRF
- Cross-Encoder Rerank
- Score Threshold
- Source Weighting
- Cross Verification
- Chunking / Semantic Chunking
- Parent Index
- Markdown Structure
- RAGAS / Faithfulness
- Answer Relevancy
- Context Recall / Precision

## Open Gaps

- 还缺少索引更新策略、chunk 粒度优化、召回评测基线、线上反馈闭环和多语言检索的面经样本。
