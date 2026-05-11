---
title: JVM Interview Outline
category: interview-outline
topic: jvm
summary: Compact study guide for current JVM object allocation and GC questions.
question_count: 2
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T10:27:14+08:00
---

# JVM Interview Outline

## Study Guide

JVM 运行时题可以围绕一条内存主线回答：对象如何进入内存，以及内存压力如何触发回收。

对象创建题的回答要像把 `new` 翻译成 JVM 内部动作。先做类加载检查，然后在堆上分配内存；内存规整时可以指针碰撞，不规整时用空闲列表；并发分配时可能用 TLAB 或 CAS。分配后先零值初始化，再设置对象头，最后执行 `<init>` 构造方法。堆里多了对象实例，当前线程栈帧里保存指向该对象的引用。

GC 触发题接在对象创建之后就很自然：对象不断进入堆，年轻代 Eden 放不下时触发 Minor GC；对象晋升或大对象进入老年代后，老年代空间不足、晋升失败、元空间不足、空间分配担保失败或显式 `System.gc()`，都可能触发 Full GC。

沿着内存主线展开，JVM 面试的初始故事就是：对象分配改变堆和栈，堆内存压力推动年轻代和老年代回收，生产排查时要关注对象分配速度、晋升情况、Full GC 频率和停顿时间。

## Interview Thread

建议按这个顺序复习：

- [[interview/questions/jvm-object-creation-memory-changes]]：先理解对象从代码进入内存的过程。
- [[interview/questions/jvm-gc-trigger-conditions]]：再理解内存压力如何触发不同级别的 GC。

这条复习线是后续类加载、对象头、TLAB、GC Roots、垃圾收集器和 GC 日志分析的入口。

## Questions

- [[interview/questions/jvm-object-creation-memory-changes]]
- [[interview/questions/jvm-gc-trigger-conditions]]

## Key Concepts

- Object Allocation
- Heap
- Stack Frame
- Object Header
- TLAB
- Eden
- Minor GC
- Full GC
- Metaspace

## Open Gaps

- 还缺少类加载、双亲委派、GC Roots、垃圾收集器、逃逸分析、JMM 和线上 GC 日志分析题。
