---
title: JVM Interview Outline
category: interview-outline
topic: jvm
summary: Lecture-style revision outline for JVM object allocation and garbage collection triggers.
question_count: 2
chapter_count: 4
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# JVM Interview Outline

## Why This Topic Matters

JVM 题常从 `new` 一个对象或 GC 触发条件开始，向内存布局、对象头、TLAB、堆分代、元空间和停顿排查延伸。核心是把 Java 代码动作翻译成 JVM 内部变化。

## Chapter 1. Object Creation Path

创建对象通常经历类加载检查、堆内存分配、零值初始化、对象头设置和执行 `<init>`。堆上增加对象实例，栈帧局部变量表保存指向对象的引用。

### Questions

- [[interview/questions/jvm-object-creation-memory-changes]]

### Concepts

- Object Allocation
- Heap
- Stack Frame
- Object Header
- TLAB

## Chapter 2. Allocation Performance And Safety

分配内存时，堆规整可以用指针碰撞，不规整则用空闲列表。并发分配需要处理线程安全，常见优化是 TLAB，让线程优先在本地分配缓冲区中快速创建对象。

## Chapter 3. GC Trigger Conditions

Minor GC 通常由 Eden 空间不足触发；Full GC 常见原因包括老年代空间不足、晋升失败、元空间不足、空间分配担保失败和显式 `System.gc()`。

### Questions

- [[interview/questions/jvm-gc-trigger-conditions]]

### Concepts

- Minor GC
- Full GC
- Eden
- Old Generation
- Metaspace

## Chapter 4. Interview Answer Pattern

JVM 题适合按“流程 -> 内存区域 -> 并发/性能 -> 生产风险”回答。比如对象创建题先讲步骤，再讲堆和栈变化；GC 题先区分 Minor GC 和 Full GC，再补线上排查方向。

## Open Gaps

- 缺少类加载、双亲委派、GC Roots、垃圾收集器、JMM、逃逸分析和线上 GC 日志分析题。
