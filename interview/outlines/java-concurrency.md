---
title: Java Concurrency Interview Outline
category: interview-outline
topic: java-concurrency
summary: Compact study guide for current Java concurrency production-risk questions.
question_count: 7
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# Java Concurrency Interview Outline

## Study Guide

Java 并发面试常见两类题：一类是“线程怎么协作收敛”（并发 fanout-fanin），另一类是“线程上下文怎么安全管理”（ThreadLocal 与线程池复用）。两类题本质都在考并发边界、失败处理和可维护性。

并发收敛题可以从 `CountDownLatch` 和 `CompletableFuture.allOf()` 两种思路讲：前者简单直接，后者更适合异步组合、异常传播、超时控制和结果聚合。只讲“等完成”不够，要讲某子任务超时、失败、部分结果返回时的策略。

线程上下文题则从线程池复用切入。Web 请求 A 在 ThreadLocal 里放了用户信息，如果请求结束没有 `remove()`，这个线程回到线程池后又处理请求 B，B 就可能读到 A 的上下文，出现串号、越权或日志链路污染。这是数据污染问题。

第二层是内存泄漏。ThreadLocalMap 的 key 是弱引用，但 value 是强引用；线程池线程长期存活时，如果没有清理，value 可能长期挂在线程上。生产规范因此很明确：能不用隐式上下文就不用；必须用时统一封装，并在 `try...finally` 中清理。

## Interview Thread

建议先复习 `[[interview/questions/concurrent-search-thread-coordination]]`，再复习 `[[interview/questions/threadlocal-business-risks]]`。前者讲任务协同收敛，后者讲线程上下文安全；组合起来更像生产并发系统的完整答题链路。

## Questions

- [[interview/questions/concurrent-search-thread-coordination]]
- [[interview/questions/threadlocal-business-risks]]
- [[interview/questions/java-lock-types-implementations]]
- [[interview/questions/java-aqs-reentrantlock]]
- [[interview/questions/java-cas-atomic-operations]]
- [[interview/questions/java-thread-pool-rejection-policy]]
- [[interview/questions/java-thread-pool-sizing-and-dynamic-tuning]]

## Key Concepts

- ThreadLocal
- Thread Pool
- CountDownLatch
- CompletableFuture
- Fanout-Fanin
- ThreadLocalMap
- Context Leakage
- Memory Leak
- `try...finally remove()`
- Synchronized
- Lock Upgrade（Biased → Lightweight → Heavyweight）
- AQS / CLH Queue
- CAS / ABA Problem
- ReentrantLock / Condition
- StampedLock
- RejectedExecutionHandler
- Backpressure
- Dynamic Thread Pool
- Nacos Config Listener
- IO-Bound Thread Sizing

## Open Gaps

- volatile、Java 内存模型（JMM）、CompletableFuture 和异步上下文传播题还缺少。
