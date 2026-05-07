---
title: Java 创建对象时 JVM 和内存发生什么变化？
category: interview-question
topic: jvm
subtopics: [object-creation, heap, stack, object-header, tlab]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - Java 创建一个对象，虚拟机会有哪些变化？JVM 的内存会有哪些变化？
summary: 考察 JVM 对象创建流程、堆内存分配、对象头初始化、构造方法和栈上引用变化。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# Java 创建对象时 JVM 和内存发生什么变化？

## Variants

- Java 创建一个对象，虚拟机会有哪些变化？
- JVM 的内存会有哪些变化？

## Short Answer

执行 `new` 时，JVM 会先做类加载检查，然后在堆上为对象分配内存，通常通过指针碰撞或空闲列表完成，并用 TLAB 或 CAS 处理并发安全。随后把对象内存初始化为零值，设置对象头，最后执行构造方法。内存变化是堆中新增对象实例，当前线程栈帧中的局部变量表保存指向该对象的引用。

## Full Answer

对象创建第一步是类加载检查。JVM 需要确认这个类是否已经被加载、解析和初始化；如果没有，就先执行类加载过程。

第二步是分配内存。对象实例数据主要放在堆中。如果堆内存规整，JVM 可以用指针碰撞快速分配；如果不规整，则通过空闲列表找到合适区域。并发创建对象时，为避免多个线程抢同一块内存，JVM 可能使用 TLAB，让每个线程先在自己的本地缓冲区分配；TLAB 不够时再走同步或 CAS。

第三步是零值初始化。JVM 会把对象内存中实例字段先设为默认零值，这也是为什么成员变量即使没有显式赋值，也有默认值。

第四步是设置对象头。对象头里包含 Mark Word、类型指针等信息，涉及哈希码、GC 分代年龄、锁状态和对象所属类元数据。

第五步才是执行 Java 层面的构造逻辑，也就是 `<init>` 方法。此时对象按程序定义完成初始化。栈上的变化通常是当前方法栈帧的局部变量表中出现一个引用变量，指向堆中的对象地址。

## Follow-ups

- 指针碰撞和空闲列表分别适用于什么堆布局？
- TLAB 如何提升对象分配性能？
- 对象头里包含哪些信息？
- 对象一定分配在堆上吗？

## Related Concepts

- 对象创建
- 堆
- 栈帧
- 对象头
- TLAB

## In Outlines

- [[interview/outlines/jvm]]
