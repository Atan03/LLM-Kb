---
title: 线上 OOM 应如何排查？Heap Dump 如何定位问题代码？
category: interview-question
topic: jvm
subtopics: [oom, heap-dump, memory-leak, jmap, mat, gc-overhead]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/jvm-gc]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 线上发生 OOM 时的排查路径是什么？如何通过快照定位到具体代码行？
summary: 考察 OOM 类型判断、Heap Dump 获取手段、以及用 MAT/VisualVM 分析快照定位泄漏根因的完整链路。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# 线上 OOM 应如何排查？Heap Dump 如何定位问题代码？

## Variants

- Java 线上 OOM 了怎么排查？
- Heap Dump 怎么获取和分析？
- OOM 常见根因有哪些？

## Short Answer

OOM 排查分三步：先根据错误类型判断是堆内存、元空间还是堆外内存；再通过 `-XX:+HeapDumpOnOutOfMemoryError` 自动 dump 或 `jmap` 手动获取快照；最后用 Eclipse MAT 或 VisualVM 分析 Leak Suspects 和 Dominator Tree 定位泄漏对象。

## Full Answer

### 第一步：确认 OOM 类型

错误日志里有明确类型：

- **`Java heap space`**：堆内存不足，最常见
- **`GC overhead limit exceeded`**：GC 时间超过 98% 但回收不到 2%，等同于堆撑满
- **`Metaspace`**：元空间不足，通常是类加载器泄漏或动态生成类过多
- **`Direct buffer memory`**：堆外内存（NIO DirectByteBuffer）不足

### 第二步：获取 Heap Dump

**生产环境提前配置**：
```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/data/heap-dump/
```

如果进程还活着，可以手动：
```bash
jmap -dump:format=b,file=heap.hprof <pid>
```

注意：dump 期间 JVM 会暂停（尤其堆大时可达几十秒），生产高峰慎用。

### 第三步：用 MAT 分析快照

Eclipse MAT 分析步骤：

1. **Leak Suspects Report**：自动找可疑泄漏点，通常直接指向问题类
2. **Dominator Tree**：按对象占用内存从大到小排序，看谁占了最多堆空间
3. **Histogram**：按类统计对象数量和内存占用，看哪个类实例数量异常多
4. **Path to GC Roots**：找到大对象后，右键 → Path to GC Roots，看为什么没被 GC 回收（谁持有了它的引用）

### 常见根因

- **大 List/Map 没有清理**：本地缓存没有 LRU 上限，持续累积
- **数据库一次性查出百万行**：MyBatis 写 `SELECT * FROM huge_table`，JVM 堆被打爆
- **线程池队列无界**：`new LinkedBlockingQueue<>()`（无界），任务堆积拖垮堆
- **类加载器泄漏**：Web 容器热部署场景，旧应用卸载后类加载器仍被引用
- **ByteBuf 没 release**：Netty DirectByteBuffer 泄漏，堆外内存持续增长
- **ThreadLocal 没有清理**：线程池复用场景，ThreadLocal value 持续累积

## Follow-ups

- OOM 之前有什么预兆可以监控？
- 如何区分内存泄漏和内存抖动？
- JProfiler 和 MAT 有什么区别？

## Related Concepts

- OOM Types
- Heap Dump
- Eclipse MAT
- GC Roots
- Memory Leak
- DirectByteBuffer
- ThreadLocal Leak

## In Outlines

- [[interview/outlines/jvm]]
