---
title: CAS 是什么？写一个实例并说明 ABA 问题。
category: interview-question
topic: java-concurrency
subtopics: [cas, atomic, aba-problem, optimistic-lock]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/java-cas]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 写一个用到 CAS 的实例
summary: 考察 CAS 原理（Unsafe + CPU 指令）、乐观锁思维、ABA 问题及解决方案。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# CAS 是什么？写一个实例并说明 ABA 问题。

## Variants

- CAS 的原理是什么？
- Java 中如何实现无锁并发？
- ABA 问题是什么？怎么解决？

## Short Answer

CAS（Compare-And-Swap）是一条 CPU 指令：比较内存值和期望值，相等则交换，不相等则重试。Java 中通过 `Unsafe.compareAndSwapInt` 调用底层 `lock cmpxchg` 指令实现原子操作。CAS 是乐观锁的核心——不上锁先操作，冲突了再重试。常见问题是 ABA 问题（值从 A 变成 B 再变回 A，CAS 察觉不到），解决用带版本号的 `AtomicStampedReference`。

## Full Answer

### CAS 原理

CAS 是 CPU 提供的一条原子指令。以 `compareAndSet(expected, new)` 为例：

1. 读取当前值 V
2. 比较 V 和 expected
3. 相等 → 把内存位置的值换成 new，返回 true
4. 不相等 → 不做任何修改，返回 false

在 Intel CPU 上对应 `lock cmpxchg` 指令（多核下自动加锁缓存行，保证原子性）。Java 中 `AtomicInteger#getAndIncrement()` 的底层就是 do-while CAS 循环：

```java
public final int getAndIncrement() {
    int oldVal;
    do {
        oldVal = unsafe.getIntVolatile(this, valueOffset);
    } while (!unsafe.compareAndSwapInt(this, valueOffset, oldVal, oldVal + 1));
    return oldVal;
}
```

### 线程安全计数器实例

```java
public class CasCounter {
    private final AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        // getAndIncrement = CAS 循环：do { old=count } while (!CAS(count, old, old+1))
        count.getAndIncrement();
    }

    // 手写 CAS 循环，等价于 getAndIncrement
    public void manualIncrement() {
        int oldVal, newVal;
        do {
            oldVal = count.get();
            newVal = oldVal + 1;
        } while (!count.compareAndSet(oldVal, newVal));
    }

    public int get() { return count.get(); }
}
```

### CAS 的三大问题

**ABA 问题**：值从 A 变成 B 再变回 A，CAS 看到"还是 A"就认为没变过，但实际中间发生了两次修改。在某些场景下会导致逻辑错误（例如栈顶弹出不等于原本预期的节点）。

解决方案：`AtomicStampedReference` 给引用附加一个整数版本号，每次修改同时更新版本。比较时不但要值相等，版本也要相等才能交换。

```java
AtomicStampedReference<Integer> ref = new AtomicStampedReference<>(0, 0);
ref.compareAndSet(0, 1, 0, 1);  // 版本从 0 变 1
```

**自旋开销**：竞争激烈时，多个线程持续 CAS 循环但不成功，CPU 空转浪费。解决方案：设置最大重试次数，或者退化成重量级锁（synchronized）。

**只能保证单个变量**：多个变量的原子性更新 CAS 无法独立完成。解决：把多个变量封装成一个对象，用 `AtomicReference<MyObj>` 原子地替换整个对象；或者使用锁。

## Follow-ups

- CAS 和 synchronized 的性能谁更好？
- `AtomicInteger` 和 `Integer` 的区别？
- `AtomicReference` 和 `AtomicInteger` 的使用场景？
- 什么是锁升级？JDK 6 做了哪些优化？

## Related Concepts

- CAS
- ABA Problem
- AtomicInteger
- AtomicStampedReference
- Optimistic Lock
- Lock-Free

## In Outlines

- [[interview/outlines/java-concurrency]]
