---
title: AQS 和 ReentrantLock 的关系是什么？
category: interview-question
topic: java-concurrency
subtopics: [aqs, reentrantlock, lock, condition, fair-unfair]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/java-aqs]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 介绍一下 AQS
  - 写一个用到 ReentrantLock 的实例
summary: 考察 AQS 骨架（state + CLH 队列）和 ReentrantLock 如何基于它实现独占/公平/多条件锁。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# AQS 和 ReentrantLock 的关系是什么？

## Variants

- 介绍一下 AQS
- ReentrantLock 的底层是什么？
- AQS 的两种模式是什么？
- ReentrantLock 和 synchronized 有什么区别？

## Short Answer

AQS（AbstractQueuedSynchronizer）是 J.U.C 大多数同步工具的实现骨架，通过一个 `volatile int state` 表示同步状态，加上一个 CLH 变体双向队列管理等待线程。ReentrantLock 基于 AQS 实现独占模式，用 CAS 修改 state 成功来获取锁，失败则入队挂起。ReentrantLock 相比 synchronized 支持公平/非公平、可中断、可超时、多条件队列。

## Full Answer

### AQS 核心结构

AQS 解决的是"把锁的状态管理和线程排队逻辑统一抽象"这个问题。它只定义了两件事：

- **`volatile int state`**：表示同步状态，具体含义由子类定义。ReentrantLock 里 state = 重入次数，Semaphore 里 state = 剩余许可数，CountDownLatch 里 state = 倒数计数。
- **CLH 变体双向队列**：获取锁失败的线程被包装成 Node 入队，`LockSupport.park()` 挂起，等待锁持有者释放时唤醒。

### 两种模式

- **独占模式（Exclusive）**：同一时刻只有一个线程能获取锁。ReentrantLock 用这个模式。
- **共享模式（Shared）**：同一时刻多个线程可以同时获取。Semaphore（许可数 N）和 CountDownLatch（N 个倒数）用这个。

### ReentrantLock 工作流程

以非公平锁为例：

1. 线程调用 `lock()`，先用 CAS 把 state 从 0 改成 1
2. 成功 → 持有锁，记录持有线程
3. 失败 → 构建 Node 入队，`LockSupport.park()` 挂起
4. 持有线程释放时调用 `unlock()`，CAS 把 state 减 1，然后 `LockSupport.unpark()` 唤醒队首线程
5. 被唤醒线程重新尝试 CAS 获取

可重入原理：同一线程再次获取时 CAS 把 state 递增，释放时 state 递减，计数器归零才真正释放锁。

**公平锁**的差别：在尝试 CAS 获取锁之前，先检查队列中是否有更早等待的线程——如果有，则乖乖去队尾排队，而非插队。代价是每次获取多一次队列查询，非激烈竞争下吞吐量略低。

### ReentrantLock 相比 synchronized 的优势

synchronized 是 JVM 内置锁，行为固定；ReentrantLock 是显式锁，功能更丰富：

- `tryLock()`：非阻塞尝试，获取不到立即返回，不排队
- `lockInterruptibly()`：可中断等待，线程在等待过程中可以被其他线程中断
- `tryLock(timeout)`：超时等待，避免无限期阻塞
- **多个 Condition**：每个 Condition 都是独立的等待队列，synchronized 只有一个隐式的等待队列
- **公平锁选项**：可选是否公平

### BoundedQueue 实例（ReentrantLock + Condition）

```java
public class BoundedQueue<T> {
    private final LinkedList<T> queue = new LinkedList<>();
    private final int capacity;
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition notFull  = lock.newCondition();
    private final Condition notEmpty = lock.newCondition();

    public void put(T item) throws InterruptedException {
        lock.lock();
        try {
            while (queue.size() == capacity) notFull.await();
            queue.addLast(item);
            notEmpty.signal();
        } finally { lock.unlock(); }
    }

    public T take() throws InterruptedException {
        lock.lock();
        try {
            while (queue.isEmpty()) notEmpty.await();
            T item = queue.removeFirst();
            notFull.signal();
            return item;
        } finally { lock.unlock(); }
    }
}
```

这个例子展示了用两个 Condition 分别管理"队列满"和"队列空"的等待/唤醒逻辑，比 synchronized + Object#wait/notify 更清晰。

## Follow-ups

- AQS 的公平锁和非公平锁性能差多少？
- 多 Condition 和 synchronized 的 wait/notify 相比有什么优势？
- Condition#await() 和 Object#wait() 有什么区别？
- ReentrantLock 如何实现可重入？

## Related Concepts

- AQS
- CLH Queue
- ReentrantLock
- Condition
- Fair vs Unfair Lock
- LockSupport

## In Outlines

- [[interview/outlines/java-concurrency]]
