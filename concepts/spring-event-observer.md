---
title: Spring Event-Driven Architecture (Observer Pattern)
category: concept
tags: [spring, observer-pattern, event-driven, application-event]
summary: Spring 事件驱动模型基于观察者模式实现模块间解耦通信，通过 ApplicationEvent 和 Listener 协作。
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

# Spring Event-Driven Architecture (Observer Pattern)

Spring 事件驱动模型是观察者模式的经典应用。三个角色：
- **事件**：`ApplicationEvent`（继承自 `java.util.EventObject`）
- **发布器**：`ApplicationEventPublisher`
- **监听器**：`ApplicationListener` 或 `@EventListener`

Spring 内置事件（继承自 `ApplicationContextEvent`）：
- `ContextStartedEvent` — 容器启动后触发
- `ContextStoppedEvent` — 容器停止后触发
- `ContextRefreshedEvent` — 容器初始化或刷新完成后触发
- `ContextClosedEvent` — 容器关闭后触发

## Key Engineering Constraints

默认事件发布是**同步**的：发布线程阻塞到所有监听器执行完毕。如果监听器中有重 IO 操作，发布性能会显著下降。解决方式：
1. `@Async` 异步执行监听器
2. `@TransactionalEventListener` 在事务提交后异步触发

事件风暴（大量事件在短时间内级联触发）和事务边界是使用事件驱动架构时需要重点控制的。

## Related Concepts

- [[concepts/spring-ioc-container]]
- [[concepts/spring-aop-proxy]]
- [[interview/questions/spring-sourcecode-design-patterns]]
- [[entities/spring-framework]]
