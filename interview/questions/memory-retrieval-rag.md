---
title: 为什么用 RAG 检索 Memory？分层检索怎么设计？
category: interview-question
topic: rag
subtopics: [memory-retrieval, vector-search, layered-recall, hybrid-search]
question_type: emerging
answer_status: reviewed
priority: high
frequency: medium
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-美团-agent开发.md
source_questions:
  - 为什么用 RAG 去检索 Memory？为什么用向量这种方式？
  - Memory 的检索是分层次的吗？具体怎么设计的？
  - 检索中遇到了什么痛点？
summary: 考察记忆检索为什么选择向量 RAG 而非关键词，分层检索设计（Profile/Episode/Knowledge），以及检索真实痛点。
created: 2026-05-11
updated: 2026-05-11
---

# 为什么用 RAG 检索 Memory？分层检索怎么设计？

## Variants

- 为什么用 RAG 去检索 Memory？
- 为什么用向量检索而不是关键词？
- Memory 检索分层次吗？怎么设计的？
- 检索中遇到哪些痛点？

## Short Answer

用 RAG 检索 Memory 是因为记忆需要语义匹配而非关键词匹配。结构化记忆走精确检索（Key-Value），文本记忆走向量检索（Embedding），两路结果合并。分层设计按记忆类型分为 Profile 层（强结构化全量注入）、Episode 层（向量检索按相关性召回）、Knowledge 层（结构化+标签索引），按时间分为 Session内、近7天热存储、长期冷存储。

## Full Answer

先说为什么用 RAG 而不是关键词来检索 memory。这个问题的本质是：query 和记忆之间的相关性是语义层面的，不是字面匹配。用户说"帮我点一份不辣的"，应该召回"用户肠胃不好不能吃辣"——这两个句子没有任何共同关键词，BM25 就 miss 了，但向量检索能捕捉"不辣"和"不能吃辣"之间的语义距离。所以选择向量不是跟风，是在这种场景下关键词检索的确做不到。

但向量不是唯一的检索手段。结构化记忆（过敏信息、地址、预算范围）用精确的 Key-Value 匹配，又快又准，不需要向量化。所以更准确的说法是：**结构化记忆走精确检索，文本记忆走向量检索，混合召回，两路结果合并注入**。

分层设计需要按两个维度来拆。

按**记忆类型**分层：最底层是 Profile 层，存用户基本属性——过敏、偏好、地址，强结构化且数量有限（固定几条），每次都全量注入，不检索。中间是 Episode 层，存历史事件型记忆——"上次用户投诉了某家店"，文本存储，向量检索按相关性召回。最上是 Knowledge 层，存用户领域知识——"该用户是素食主义者"，介于两者之间，结构化+标签索引，混合检索。

按**时间**分层：Session 内的记忆直接放 Conversation History，不走 Memory 系统。跨 Session 短期（近 7 天）放热存储，优先召回。长期记忆放冷存储，相关性权重略低，避免太久远的信息干扰当前场景。检索时按层级并发查询，结果按相关性分数 + 时间衰减因子排序取 topK。

最后说几个真实踩过的坑，面试里讲出来非常加分。

**向量漂移**：用户的一句话有歧义，embedding 到语义空间后召回了矛盾的记忆。比如"我不喜欢太甜的"召回了"用户喜欢甜品"——"甜"这个概念在向量空间里把矛盾双方拉近了。解法是加关键词过滤做二次筛选，不能纯靠向量。

**冷启动**：新用户没有任何记忆，向量检索为空。需要默认 profile 兜底，或者在前几轮对话里做主动引导收集信息。

**记忆老化**：用户三年前的偏好现在变了，但记忆还在，被召回后反而干扰模型。加时间戳和衰减权重，太老的降权或归档。

**检索延迟**：记忆检索是在用户发消息后、模型生成前的串行步骤，直接影响首 token 延迟。解法是预取——在用户输入时就异步触发检索，同时做检索结果缓存。

## Follow-ups

- 向量漂移的二次筛选具体怎么做？
- 冷启动阶段记忆获取的策略？
- 记忆老化怎么判断"太旧"的阈值？

## Related Concepts

- Vector Search vs BM25
- Hybrid Retrieval
- Memory Age Decay
- Cold Start
- Embedding Drift

## In Outlines

- [[interview/outlines/rag]]
