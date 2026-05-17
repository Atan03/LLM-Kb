---
title: Spring Singleton Registry
category: concept
tags: [spring, singleton, bean-scope, concurrent-hashmap]
summary: Spring 通过 ConcurrentHashMap 实现单例注册表，bean 默认作用域为 singleton，从容器级别保证单例。
base_confidence: 1.0
lifecycle: draft
lifecycle_changed: 2026-05-10
provenance:
  extracted: 0.9
  inferred: 0.1
  ambiguous: 0.0
sources:
  - /Users/atan/Documents/llm-wiki-sources/blog-posts/Spring 中的设计模式详解.pdf
created: 2026-05-10
updated: 2026-05-10
---

# Spring Singleton Registry

Spring 中 bean 的默认作用域是 `singleton`。底层通过 `ConcurrentHashMap` 实现线程安全的单例注册表：

```java
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(64);
```

核心逻辑：`getSingleton()` 先检查缓存，不存在时通过 `synchronized` 块创建，然后通过 `addSingleton()` 注册到单例注册表中。

Bean 作用域包括：
- `singleton` — 默认，容器级单例
- `prototype` — 每次 `getBean()` 返回新实例
- `request` — 每个 HTTP 请求一个实例
- `session` — 每个 HTTP Session 一个实例
- `application/global-session` — 每个 Web 应用启动时一个实例
- `websocket` — 每个 WebSocket 会话一个实例

## Thread Safety Concern

单例 Bean 存在线程安全问题。解决方式：
1. 尽量避免定义可变的成员变量（无状态 Bean）
2. 使用 `ThreadLocal` 保存可变成员变量

大部分 Service、DAO 属于无状态 Bean，天然线程安全。

## Comparison with GoF Singleton

Spring 的单例是**容器级单例**，而非 JVM 级。同一个 JVM 中可以运行多个 Spring 容器，每个容器各自维护自己的单例缓存。这个区别在面试时经常被问到。

## Related Concepts

- [[concepts/spring-ioc-container]]
- [[concepts/spring-template-method-callback]]
- [[interview/questions/spring-sourcecode-design-patterns]]
- [[entities/spring-framework]]
