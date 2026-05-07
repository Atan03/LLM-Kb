---
title: Java Concurrency Interview Outline
category: interview-outline
topic: java-concurrency
summary: Lecture-style revision outline for Java concurrency production pitfalls.
question_count: 1
chapter_count: 3
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# Java Concurrency Interview Outline

## Why This Topic Matters

Java 并发题不只是 API 背诵，更关注线程池、上下文传递、生命周期和生产事故。ThreadLocal 是高频切口，因为它看似方便，实际很容易在池化线程里造成串号和内存泄漏。

## Chapter 1. Thread-Scoped State Is Dangerous In Pools

ThreadLocal 的语义是线程内变量，但 Web 服务里的线程会复用。如果请求结束后不清理，下一次复用同一线程的请求可能读到旧用户、旧租户或旧 trace 上下文。

### Questions

- [[interview/questions/threadlocal-business-risks]]

### Concepts

- ThreadLocal
- Thread Pool
- Context Leakage
- Memory Leak

## Chapter 2. Cleanup Must Be A Contract

如果必须使用 ThreadLocal，应封装统一入口，并在 `try...finally` 中调用 `remove()`。跨线程异步任务要特别小心，因为上下文不会天然安全传播，强行传播也可能扩大污染范围。

## Chapter 3. Interview Answer Pattern

回答 ThreadLocal 风险时，要把“线程池复用导致数据污染”和“ThreadLocalMap value 强引用导致泄漏”分开讲，再落到生产规范：少用、封装、finally 清理、避免存大对象。

## Open Gaps

- 缺少锁、AQS、线程池参数、CompletableFuture、volatile 和 Java 内存模型题。
