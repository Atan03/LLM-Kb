---
title: 并发搜索中主线程如何与子线程协同收敛？
category: interview-question
topic: java-concurrency
subtopics: [countdownlatch, completablefuture, fanout-fanin, timeout, exception]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-淘宝天猫-agent开发.md
source_questions:
  - 并发搜索场景下，主线程起了多个子线程后，怎么和它们通信以知道任务全都做完了？
summary: 考察并发 fanout-fanin 场景下的线程协同、任务收敛、异常与超时处理。
created: 2026-05-09T10:43:12+08:00
updated: 2026-05-09T10:43:12+08:00
---

# 并发搜索中主线程如何与子线程协同收敛？

## Variants

- 主线程起了多个子线程后，怎么知道任务都做完了？
- 并发搜索的结果如何聚合？

## Short Answer

基础方案可用 `CountDownLatch` 做“计数归零”同步；更现代方案常用 `CompletableFuture.allOf()` 做 fanout-fanin 聚合。工程上要补齐超时、异常、降级和部分结果策略，否则只解决了“等完成”，没有解决“可用性”。

## Full Answer

我在生产里一般把这类并发搜索拆成“收敛协议”问题，而不是“用哪个 API”问题。流程是：主线程先按下游能力做并发预算（比如最多 8 或 16 个并发分支），然后 fanout 到多个检索源，再 fanin 汇总。这里第一件事不是等全部成功，而是定义“什么叫可用返回”：必须全成功、允许部分成功、还是达到 N 个有效结果就提前返回。

`CountDownLatch` 适合固定批次、一次性任务，例如同一请求要打 5 个内部索引，全部返回后统一 merge。它简单稳，但不擅长表达复杂异常路径。`CompletableFuture` 更适合线上复杂链路，因为我可以给每个分支挂超时、降级、重试和熔断策略，再在 `allOf` 之后做结果分层：成功结果、可重试失败、永久失败。

真实项目里最容易踩坑的是“慢分支拖垮整体 P99”。我的处理是分层超时：单分支 200~400ms，整体请求 600~900ms；单分支超时直接标记为 degraded，不阻塞整体返回。其次是失败隔离：某个搜索源连续超时时自动降权或临时摘除，避免全链路被一个坏节点拖死。再次是容量保护：通过线程池隔离和 semaphore 限流，防止请求高峰把下游压崩。

结果聚合也不能只拼列表。通常要做去重、来源打分、时效加权、质量阈值过滤。比如电商搜索里，主索引结果权重高于补充索引；若主索引为空才放开召回阈值。最后监控指标要闭环：分支成功率、分支超时率、整体耗时分位、部分结果返回率、降级触发率。面试时把这套“收敛策略 + 保护机制 + 指标闭环”讲出来，可信度会明显高于只说 `allOf().join()`。

## Follow-ups

- `CountDownLatch` 和 `CompletableFuture.allOf()` 怎么选？
- 某个子任务超时或异常时，主流程怎么办？
- 是否支持部分结果先返回？

## Related Concepts

- Fanout-Fanin
- CountDownLatch
- CompletableFuture
- Timeout
- Partial Result

## In Outlines

- [[interview/outlines/java-concurrency]]
