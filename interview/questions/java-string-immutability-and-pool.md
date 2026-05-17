---
title: Java String 有哪些关键特性？为什么 StringBuilder 比 StringBuffer 快？
category: interview-question
topic: backend-system-design
subtopics: [java, string, immutability, string-pool, performance]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/java-string-pool]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 介绍 String，知道多少说多少
  - 介绍 StringBuffer、StringBuilder 的不同以及 Buffer 线程安全的原因
summary: 考察 String 不可变性原理、字符串常量池机制、Compact Strings 优化，以及 StringBuffer/StringBuilder 选型。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# Java String 有哪些关键特性？为什么 StringBuilder 比 StringBuffer 快？

## Variants

- String 为什么是不可变的？
- 字符串常量池是什么？
- StringBuilder 和 StringBuffer 的区别？
- String 拼接和 StringBuilder 性能差多少？

## Short Answer

String 的不可变性由 `final char[]`（Java 9 后是 `byte[]` + 编码标记）保证，使 String 线程安全且可安全用作 HashMap key。字符串常量池通过复用字面量节省内存。StringBuilder（非同步）比 StringBuffer（同步）快，因为省去了每次操作获取和释放锁的开销；在没有多线程共享写场景下，应优先使用 StringBuilder。

## Full Answer

### 不可变性

String 内部用 `final char[]`（Java 9 之后是 `byte[]` + 编码标记）存储字符序列。`final` 保证了数组引用不可变，而 String 没有对外暴露修改数组内容的 public 方法——所有"修改"操作（`substring`、`replace`、`concat` 等）都返回新 String，不修改原对象。

不可变性带来的实际收益有三个：线程安全（多线程共享 String 不需要同步）、可作为 HashMap key（不会被修改导致哈希变化）、字符串常量池复用（字面量只存一份，全局共享）。

### 字符串常量池

字面量形式（`"abc"`）创建的 String 会进入堆内的字符串常量池（JDK 7 后从 PermGen 移到堆，常驻）。同样内容的字面量只存一份，节省内存。

关键点：`new String("abc")` 会在堆上创建一个新对象，这个对象和常量池中的"abc"是两个独立引用；`String#intern()` 可以手动把堆上的字符串放入常量池，在 JDK 7+ 后 Interned String 会被移到堆里。

### Java 9 Compact Strings

如果字符串内容全是 Latin-1 字符（单字节字符），Java 9 后改用 `byte[]` 存储，一个字符只占 1 字节而非 2 字节（char[] 时期）。这是重要的内存优化，减少了约一半的字符串内存占用。

### String vs StringBuilder vs StringBuffer

三者对比：

| | String | StringBuffer | StringBuilder |
|--|--|--|--|
| 可变性 | 不可变 | 可变 | 可变 |
| 线程安全 | 安全（不可变） | 安全（方法级 synchronized） | 不安全 |
| 性能 | 频繁拼接最差 | 中（带锁） | 最快（无锁） |
| 使用场景 | 常量、少拼接 | 多线程共享写 | 单线程拼接 |

StringBuffer 的线程安全靠的是方法级 `synchronized`——所有 public 方法都加了锁，保证同一时刻只有一个线程能执行任何修改操作。这和 `volatile` 保证的"可见性"不同：`volatile` 只保证读写看到最新值，但不保证复合操作（如 ++）的原子性；`synchronized` 则保证了整个方法的原子性。

StringBuilder 是 StringBuffer 的无锁版本，适用于没有多线程共享写需求的场景。现代 Java 代码里，如果并发问题已经被其他机制控制，StringBuilder 是首选。

**容易被忽略的性能陷阱**：编译器会优化单个语句内的 `+` 拼接为 StringBuilder，但跨语句（比如循环）不会。所以循环内拼接字符串要显式使用 StringBuilder，否则是 O(n²) 复杂度。

## Follow-ups

- `String#intern()` 在 JDK 7 后发生了什么变化？
- String 可以作为 HashMap key 还需要重写 hashCode 吗？
- `String s = new String("abc")` 创建了几个对象？
- 为什么说 String 是 immutable 但 `String s = "a" + "b"` 创建了新对象？

## Related Concepts

- String Immutability
- String Pool
- Compact Strings
- Synchronized vs Volatile
- String Concatenation Performance

## In Outlines

- [[interview/outlines/backend-system-design]]
