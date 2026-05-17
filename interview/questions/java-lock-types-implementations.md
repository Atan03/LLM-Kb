---
title: Java 锁有哪些种类？底层分别是怎么实现的？
category: interview-question
topic: java-concurrency
subtopics: [synchronized, reentrantlock, readwritelock, stampedlock, optimistic-lock]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/java-synchronized]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 锁的种类以及底层实现
summary: 考察 synchronized 锁升级、AQS 锁分类、悲观乐观锁思想，以及各种锁的选型依据。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# Java 锁有哪些种类？底层分别是怎么实现的？

## Variants

- synchronized 底层是怎么实现的？
- synchronized 和 ReentrantLock 的区别？
- 偏向锁、轻量级锁、重量级锁是什么？
- 乐观锁和悲观锁的区别？

## Short Answer

按实现分：synchronized 是 JVM 内置锁，基于对象头 Mark Word + Monitor 实现，JDK 6 后有锁升级（无锁→偏向锁→轻量级锁→重量级锁）；ReentrantLock 基于 AQS，用 CAS + 队列实现。按特性分：公平/非公平、可重入（同一线程多次获取）、读写锁（ReadWriteLock/StampedLock）、乐观锁（CAS 不上锁）。synchronized 和 ReentrantLock 都是悲观锁——先取锁再操作；乐观锁不上锁，操作完用 CAS 检测冲突。

## Full Answer

### 按实现分：synchronized vs ReentrantLock

**synchronized**：JVM 内置锁。每个 Java 对象都有一个 Monitor 对象（关联对象头 Mark Word）。线程进入 synchronized 代码块时，需要获取该对象的 Monitor——成功获取则进入，失败则阻塞等待。

JDK 6 引入锁升级机制，这是面试重点：

- **无锁状态**：对象头初始状态，无线程竞争
- **偏向锁**：第一个获取该锁的线程在 Mark Word 里记录自己的线程 ID，之后该线程进入同步块不再需要 CAS，直接检查 Mark Word 里的线程 ID——开销最小，只适合无竞争场景
- **轻量级锁**：有竞争时（另一个线程尝试获取但当前未持有），升级为轻量级锁。获取锁的线程在当前线程栈帧里创建锁记录（Lock Record），用 CAS 把 Mark Word 替换为指向锁记录的指针。竞争线程自旋等待，不阻塞线程
- **重量级锁**：竞争加剧（自旋次数超限或等待线程多），膨胀为重量级锁。未获取锁的线程被 OS 互斥量挂起，需要内核态切换，吞吐量明显下降

锁只能升级不能降级，这是单向过程。

**ReentrantLock**：J.U.C 中的显式锁，基于 AQS 实现。支持公平/非公平、可中断、可超时、多条件队列，是 synchronized 的功能超集。

### 按特性分

**公平锁 vs 非公平锁**：公平锁按等待队列 FIFO 顺序获取，非公平锁允许插队（一个新线程尝试 CAS 直接抢锁）。非公平锁吞吐量大，但可能导致等待线程饥饿。

**可重入锁（Reentrant）**：同一线程在外层已获取锁的情况下，内层重复获取不会死锁——因为底层用一个计数器记录重入次数，每次获取递增，释放递减。synchronized 和 ReentrantLock 都是可重入的。

**读写锁（ReentrantReadWriteLock）**：读读不互斥、读写互斥、写写互斥。适合读多写少场景——读操作可以并发进行，只有写操作需要独占。缺点是写操作饥饿风险。

**StampedLock**：Java 8 引入，比 ReadWriteLock 更进一步。特点是有乐观读模式——不加锁直接读，读完用 Stamp（版本号）验证，如果中间有其他线程写了，验证失败则重试。这在大多数情况下避免了读操作加锁开销，吞吐量最高。

### 乐观锁 vs 悲观锁

这是设计思想，不是具体实现：

- **悲观锁**：假设冲突概率高，先取锁再操作。synchronized 和 ReentrantLock 是悲观锁
- **乐观锁**：假设冲突概率低，不上锁，操作完用 CAS 校验是否有冲突。AtomicInteger、AtomicStampedReference 是乐观锁

## Follow-ups

- 锁升级的过程是什么？哪些条件触发升级？
- 为什么说非公平锁吞吐量更高但可能饥饿？
- StampedLock 的乐观读怎么验证版本？
- 读写锁在读多写少场景下的具体收益？

## Related Concepts

- Synchronized
- Mark Word
- Lock Upgrade（Biased → Lightweight → Heavyweight）
- AQS
- ReentrantReadWriteLock
- StampedLock
- Optimistic vs Pessimistic Lock

## In Outlines

- [[interview/outlines/java-concurrency]]
