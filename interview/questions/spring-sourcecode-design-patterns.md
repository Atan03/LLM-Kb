---
title: Spring/SpringBoot 源码中有哪些典型设计模式？
category: interview-question
topic: backend-system-design
subtopics: [spring, beanfactory, proxy, template-method, observer]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/spring-ioc-container]]"
  - "[[concepts/spring-aop-proxy]]"
  - "[[concepts/spring-singleton-registry]]"
  - "[[concepts/spring-template-method-callback]]"
  - "[[concepts/spring-event-observer]]"
sources:
  - /Users/atan/Obsidian/LLM-Kb/interview/sources/20260509-快手-agent后端技术面.md
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260509-快手-agent开发.md
  - /Users/atan/Documents/llm-wiki-sources/blog-posts/Spring 中的设计模式详解.pdf
source_questions:
  - 看过 Springboot/Spring 的源码嘛?里面用到了哪些设计模式
  - 注解的实现方法，写一个具体注解实现
  - Spring 中构造类要满足的条件？
summary: 考察 Spring 核心模式：工厂、代理、模板方法、观察者及其在 IOC/AOP/事件机制中的应用，以及注解实现和 Bean 构造约束。
created: 2026-05-09T11:17:19+08:00
updated: 2026-05-11T17:00:00+08:00
---

# Spring/SpringBoot 源码中有哪些典型设计模式？

## Variants

- Spring 源码里有哪些设计模式？
- 这些模式在 Spring 里分别解决什么问题？

## Short Answer

Spring 里最关键的模式是工厂（BeanFactory/ApplicationContext）、代理（AOP、事务）、模板方法（JdbcTemplate 等）和观察者（ApplicationEvent）。这些模式分别对应对象创建治理、横切能力增强、样板代码抽象和模块解耦通信。

## Full Answer

这道题如果上来就报菜名——"Spring 用到了工厂、单例、代理、模板方法、观察者……"——那就背八股了。面试官下一句话一定是："那你说说每个模式解决了 Spring 的什么问题？"所以回答的时候不要按模式来讲，要按"Spring 遇到的问题"来讲，模式只是解法。

第一个问题：对象创建和管理散乱。一个项目里成千上万个 Bean，谁创建、谁销毁、依赖关系怎么管？Spring 的核心解法是工厂模式。BeanFactory 和 ApplicationContext 把"对象从哪来、怎么组装、生命周期怎么管"这三个问题全封装了，业务代码只管 declare，容器管 create。这就是 IOC 的本质——控制反转不是玄学，就是把"谁创建对象"这个控制权从业务代码移到了容器。你要是能顺带说一下 Bean 的三级缓存解决循环依赖——singletonFactories、earlySingletonObjects、singletonObjects——那就直接加了深度分。

第二个问题：横切逻辑侵入业务代码。事务、日志、安全、限流——这些东西和业务逻辑本身没关系，但如果不用代理模式，你只能在每个方法里硬编码，代码就烂了。Spring AOP 的底层就是代理模式：JDK 动态代理（基于接口）、CGLIB（基于继承）。关键 insight 不是"Spring 用了代理"，而是"Spring 把代理做成了一个可配置的切面框架"。这里必讲的一个坑：同类内部调用会绕过代理。比如一个类里 methodA 调 methodB，methodB 上有 @Transactional，这个事务是不会生效的——因为 this.methodB() 不是通过代理对象调用的。你提这个坑，面试官就知道你真的 debug 过。

第三个问题：样板代码重复。JDBC 操作你每次都写 try-catch-finally、连接获取、异常转换、资源释放——写一百遍人都麻了。Spring 的解法是模板方法模式。JdbcTemplate 把固定骨架（连接管理、异常处理、资源释放）写好，只留一个可变点让你填 SQL 和结果映射。RestTemplate 同理，HTTP 连接池、超时、异常转换全封装好了。这里的关键设计是：骨架代码里的可变点靠的是回调接口，而不是继承——所以它是模板方法思想 + 策略模式的混合，比经典的 GoF 模板方法更灵活。

第四个问题：模块间耦合。订单模块修改了状态，怎么通知库存模块、物流模块？如果直接调用，模块间就耦合死了。Spring 用观察者模式——ApplicationEventPublisher 发事件，各个模块通过 @EventListener 或者实现 ApplicationListener 来订阅。这里也有坑：默认情况下事件是同步的，发布事件的线程会阻塞到所有 listener 执行完。所以如果你的 listener 做了重 IO，事件发布会非常慢。解决办法是加 @Async 或者用事务事件 @TransactionalEventListener，在事务提交之后再异步发。

最后总结一句： 面试官问"Spring 里有哪些设计模式"不是想听你报菜名，而是想看你能不能把模式和工程问题对应起来。你能说出"每个模式分别解决了 Spring 的什么具体问题"，并且顺带点出几个生产踩过的坑，这就是一个能让面试官在评分表上写加分项的回答。

## Follow-ups

- 为什么 `@Transactional` 会失效？
- JDK 动态代理和 CGLIB 怎么选？
- 事件驱动在 Spring 里有哪些边界？

## Related Concepts

- [[concepts/spring-ioc-container]]
- [[concepts/spring-singleton-registry]]
- [[concepts/spring-aop-proxy]]
- [[concepts/spring-template-method-callback]]
- [[concepts/spring-event-observer]]

## In Outlines

- [[interview/outlines/backend-system-design]]
