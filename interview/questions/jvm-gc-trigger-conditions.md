---
title: JVM 垃圾回收的触发条件有哪些？
category: interview-question
topic: jvm
subtopics: [gc, minor-gc, full-gc, metaspace]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - 垃圾回收的触发条件
summary: 考察 Minor GC、Full GC 的常见触发条件和生产规避点。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# JVM 垃圾回收的触发条件有哪些？

## Variants

- 垃圾回收的触发条件。

## Short Answer

Minor GC 通常在年轻代 Eden 区空间不足、无法继续分配新对象时触发。Full GC 常见触发条件包括老年代空间不足、对象晋升失败、元空间不足、空间分配担保失败，以及显式调用 `System.gc()`。生产中应重点关注 Full GC 的频率和停顿时间。

## Full Answer

年轻代回收一般称为 Minor GC 或 Young GC。最典型触发条件是 Eden 区空间不足：新对象需要分配，但 Eden 放不下，于是触发年轻代回收，把存活对象复制到 Survivor 或晋升到老年代。

Full GC 的触发条件更复杂，影响也更大。第一类是老年代空间不足，比如大对象直接进入老年代，或者年轻代对象经过多次回收后晋升时老年代放不下。第二类是元空间不足，类元数据加载过多时可能触发。第三类是空间分配担保失败，也就是 JVM 预测老年代剩余空间不足以容纳年轻代可能晋升的对象。第四类是显式调用 `System.gc()`，虽然它只是建议，但很多配置下会导致一次较重的 GC。

面试回答可以补充生产视角：Minor GC 频繁通常说明对象创建速度快或年轻代配置不合理；Full GC 频繁则需要重点排查老年代增长、内存泄漏、大对象、类加载泄漏和 GC 参数配置。

## Follow-ups

- Minor GC 后对象什么时候晋升老年代？
- 为什么 Full GC 对延迟敏感业务影响大？
- 如何排查频繁 Full GC？

## Related Concepts

- Minor GC
- Full GC
- Eden
- 老年代
- Metaspace

## In Outlines

- [[interview/outlines/jvm]]
