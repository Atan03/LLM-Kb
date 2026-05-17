---
title: Spring IoC Container
category: concept
tags: [spring, ioc, dependency-injection, container]
summary: Spring IoC 容器通过工厂模式管理对象的创建、组装和生命周期，实现控制反转与依赖注入。
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

# Spring IoC Container

IoC（Inversion of Control，控制反转）是一种解耦的设计思想，而非具体模式。其核心目的是借助"第三方容器"管理具有依赖关系的对象，从而降低代码耦合度。DI（Dependency Injection，依赖注入）是实现 IoC 的一种设计模式。

Spring 通过 `BeanFactory` 和 `ApplicationContext` 两个核心接口实现 IoC 容器。

`BeanFactory`：延迟注入，使用到某个 bean 时才创建，占用内存更少，启动速度更快。
`ApplicationContext`：容器启动时一次性创建所有 bean，扩展了 `BeanFactory`，提供 AOP、事件发布、国际化等额外功能。

`ApplicationContext` 的三种常见实现：
- `ClassPathXmlApplicationContext` — 从类路径加载 XML
- `FileSystemXmlApplicationContext` — 从文件系统加载 XML
- `XmlWebApplicationContext` — 从 Web 系统加载 XML

IoC 的本质：对象 a 依赖对象 b，在没有 IoC 时，a 必须自己创建 b；引入 IoC 容器后，a 只需声明依赖，容器负责创建并注入 b。这个控制权转移的过程就是"控制反转"。

## Related Concepts

- [[concepts/spring-singleton-registry]]
- [[concepts/spring-aop-proxy]]
- [[entities/spring-framework]]
- [[entities/spring-boot]]
