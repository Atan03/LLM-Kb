---
title: Spring Template Method with Callback
category: concept
tags: [spring, template-method, callback, jdbctemplate]
summary: Spring 使用 Template + Callback 模式替代传统的继承式模板方法，在固定骨架中插入可变逻辑。
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

# Spring Template Method with Callback

传统的 GoF 模板方法模式通过继承实现：父类定义算法骨架，子类重写抽象方法。Spring 对此做了改进——使用 **Template + Callback** 模式。

核心区别：
- 传统模板方法：子类继承父类，重写抽象方法
- Spring 模板方法：父类定义骨架，通过回调接口让调用方注入可变逻辑

`JdbcTemplate`：固定骨架包括连接管理、异常转换、资源释放；可变点是 SQL 执行和结果映射。
`RestTemplate`：固定骨架包括 HTTP 连接池、超时管理、异常转换；可变点是请求构建和响应处理。

Callback 模式的优势：无需为了定制逻辑而创建子类，通过匿名内部类或 Lambda 即可注入可变行为，更加灵活。

## Related Concepts

- [[concepts/spring-ioc-container]]
- [[concepts/spring-singleton-registry]]
- [[interview/questions/spring-sourcecode-design-patterns]]
- [[entities/spring-framework]]
