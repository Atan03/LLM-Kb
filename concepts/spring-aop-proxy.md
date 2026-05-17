---
title: Spring AOP and Dynamic Proxy
category: concept
tags: [spring, aop, proxy, jdk-proxy, cglib]
summary: Spring AOP 基于动态代理实现横切关注点分离，JDK Proxy 用于接口代理，CGLIB 用于类代理。
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

# Spring AOP and Dynamic Proxy

AOP（Aspect-Oriented Programming）将事务、日志、权限等与业务无关的横切逻辑封装起来，避免重复代码，降低模块耦合。

Spring AOP 基于**动态代理**实现：
- **JDK Dynamic Proxy**：目标对象实现了接口时使用，通过 `InvocationHandler` 生成代理对象
- **CGLIB**：目标对象未实现接口时使用，通过生成子类字节码实现代理

Spring AOP 属于**运行时增强**（代理），AspectJ 属于**编译时增强**（字节码操作）。Spring AOP 集成了 AspectJ，但两者定位不同：Spring AOP 更简洁，AspectJ 功能更强大。当切面数量很多时，AspectJ 性能更优。

## Key Engineering Insight

同类内部调用 `this.methodB()` 会绕过代理，导致 `@Transactional`、`@Async` 等注解失效。这是因为 `this` 指向的是原始对象而非代理对象。

## Related Concepts

- [[concepts/spring-ioc-container]]
- [[interview/questions/spring-sourcecode-design-patterns]]
- [[entities/spring-framework]]
- [[entities/spring-boot]]
