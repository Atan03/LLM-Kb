---
title: Spring Boot
category: entity
tags: [spring, spring-boot, java]
summary: Spring 生态的应用开发层，提供自动配置与约定优于配置，降低工程启动成本。
base_confidence: 0.75
lifecycle: draft
lifecycle_changed: 2026-05-10
provenance:
  extracted: 0.5
  inferred: 0.5
  ambiguous: 0.0
sources:
  - /Users/atan/Documents/llm-wiki-sources/blog-posts/Spring 中的设计模式详解.pdf
created: 2026-05-10
updated: 2026-05-10
---

# Spring Boot

Spring Boot 是 Spring Framework 的工程化应用层，核心目标是减少样板配置、快速启动服务。它并不替代 Spring 的核心机制，而是把容器、自动配置、starter 依赖和运行时管理整合成更可落地的开发体验。

在当前主题中，Spring Boot 常作为“模式落地载体”：底层仍然依赖 Spring 的工厂、代理、模板方法与事件机制。

## Practical Relevance

面试里如果被问到 Spring Boot 与 Spring 的关系，可以用一句话收束：Spring 解决能力抽象，Boot 解决工程交付速度；理解源码模式时看 Spring，理解项目落地时看 Boot。

## Related

- [[entities/spring-framework]]
- [[concepts/spring-ioc-container]]
- [[concepts/spring-aop-proxy]]
