---
title: Spring Framework
category: entity
tags: [spring, java, framework]
summary: Java 企业级开发框架，核心提供 IoC、AOP、事件机制与数据访问抽象。
base_confidence: 0.85
lifecycle: draft
lifecycle_changed: 2026-05-10
provenance:
  extracted: 0.7
  inferred: 0.3
  ambiguous: 0.0
sources:
  - /Users/atan/Documents/llm-wiki-sources/blog-posts/Spring 中的设计模式详解.pdf
created: 2026-05-10
updated: 2026-05-10
---

# Spring Framework

Spring Framework 是 Java 后端生态里的核心基础框架。其最重要的工程价值在于：把对象创建、横切增强、事件解耦、数据访问模板化这些“重复且易错”的基础能力，沉到统一容器和抽象层里。

在这份知识库里，Spring 的理解主线是四块：
- IoC 容器：[[concepts/spring-ioc-container]]
- AOP 与动态代理：[[concepts/spring-aop-proxy]]
- 单例注册表与作用域：[[concepts/spring-singleton-registry]]
- 事件驱动模型：[[concepts/spring-event-observer]]

## Practical Relevance

面试中谈 Spring 不应停留在“背模式名”。更高质量的表达是：这个模式在 Spring 里解决了哪个具体工程问题，以及对应的常见坑是什么（例如事务代理失效、同步事件阻塞、单例状态线程安全）。

## Related

- [[entities/spring-boot]]
- [[interview/questions/spring-sourcecode-design-patterns]]
