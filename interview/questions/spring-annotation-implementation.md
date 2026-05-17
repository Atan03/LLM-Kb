---
title: 注解是怎么实现的？写一个具体注解的完整实现。
category: interview-question
topic: backend-system-design
subtopics: [annotation, spring, aop, reflection, java基础]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/spring-aop-proxy]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 注解的实现方法，写一个具体注解实现
summary: 考察注解本质（元注解、APT vs 反射）、AOP 拦截注解的切面实现，以及关键元注解含义。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# 注解是怎么实现的？写一个具体注解的完整实现。

## Variants

- Java 注解的原理是什么？
- 如何自定义一个注解？
- `@Target`、`@Retention` 是什么意思？
- 注解和普通接口有什么区别？

## Short Answer

注解本质是继承了 `java.lang.annotation.Annotation` 的特殊接口。注解本身不包含逻辑，逻辑由处理注解的代码（反射或 APT 编译时处理器）实现。关键元注解：`@Target` 标注注解作用位置，`@Retention` 决定注解在哪个阶段保留（RUNTIME 才能反射读取）。

## Full Answer

### 注解的本质

用 `@interface` 定义注解，编译后生成一个继承 `java.lang.annotation.Annotation` 的接口。所以注解就是接口，注解属性就是接口方法。例如：

```java
public @interface RateLimit {
    int qps() default 10;
    String key() default "";
}
```

编译后等价于：

```java
public interface RateLimit extends Annotation {
    int qps() default 10;
    String key() default "";
}
```

### 元注解

`@Target`：标注注解可以用在哪里。常见值：
- `ElementType.METHOD` — 方法
- `ElementType.TYPE` — 类/接口
- `ElementType.FIELD` — 字段

`@Retention`：标注注解在哪个阶段保留：
- `SOURCE` — 源码阶段，编译后丢弃
- `CLASS` — 字节码阶段，运行时不可见（反射读不到）
- `RUNTIME` — 运行阶段，反射可读

`@Documented`：是否包含在 Javadoc 中。

### 注解处理的两种方式

**反射处理（运行时）**：运行时通过 `Method#getAnnotation(AnnotationType.class)` 读取注解，做逻辑增强。Spring AOP、Swagger 都是这种方式。

**APT（Annotation Processing Tool，编译时）**：在编译期通过编译器插件处理注解，生成额外代码（如 Lombok 生成 getter/setter、Dagger 生成工厂类）。编译时处理不会影响运行时性能。

### 完整实例：@RateLimit 限流注解 + AOP 切面

```java
// 1. 定义注解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface RateLimit {
    int qps() default 10;
    String key() default "";
}

// 2. 切面处理注解逻辑
@Aspect
@Component
public class RateLimitAspect {
    private final Map<String, RateLimiter> limiters = new ConcurrentHashMap<>();

    @Around("@annotation(rateLimit)")
    public Object around(ProceedingJoinPoint pjp, RateLimit rateLimit) throws Throwable {
        String key = rateLimit.key().isEmpty()
            ? pjp.getSignature().toLongString()
            : rateLimit.key();

        RateLimiter limiter = limiters.computeIfAbsent(
            key, k -> RateLimiter.create(rateLimit.qps())
        );

        if (!limiter.tryAcquire()) {
            throw new RuntimeException("请求过于频繁，请稍后重试");
        }
        return pjp.proceed();
    }
}

// 3. 使用
@RestController
public class OrderController {
    @RateLimit(qps = 5, key = "createOrder")
    @PostMapping("/order")
    public String createOrder() { return "success"; }
}
```

关键点：注解定义本身只有元数据和默认值，实际逻辑在切面（`@Aspect`）里——这就是"注解本身不包含逻辑，逻辑在处理器"的意思。`@Around("@annotation(rateLimit)")` 是 AspectJ 语法，表示拦截所有带 `@RateLimit` 的方法。

## Follow-ups

- `@Transactional` 失效的常见原因有哪些？
- 编译时注解和运行时注解的性能差异？
- Lombok 的 `@Data` 为什么不生成无参构造？
- 注解可以被继承吗？

## Related Concepts

- Annotation
- AOP / AspectJ
- Reflection
- `@Target` / `@Retention`
- APT（编译时注解处理）
- Spring AOP

## In Outlines

- [[interview/outlines/backend-system-design]]
