---
title: Java 线程池有哪些拒绝策略？如何自定义？
category: interview-question
topic: java-concurrency
subtopics: [thread-pool, rejected-execution-handler, backpressure, metrics]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/java-thread-pool]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - Java 线程池如何自定义拒绝策略？
summary: 考察 JDK 四种内置拒绝策略的适用场景，以及生产级自定义拒绝策略（告警+降级+重试）的实现。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# Java 线程池有哪些拒绝策略？如何自定义？

## Variants

- 线程池拒绝策略有哪些？
- 生产环境为什么四种内置策略不够用？
- 如何写一个自定义拒绝策略？

## Short Answer

JDK 内置四种：`AbortPolicy`（抛异常）、`CallerRunsPolicy`（反压调用方）、`DiscardPolicy`（静默丢弃）、`DiscardOldestPolicy`（丢弃最老任务）。生产上前三种都不够用，通常自定义告警+降级策略，在拒绝时打日志、上报监控、落库补偿或切换同步执行。

## Full Answer

### JDK 内置四种策略

`RejectedExecutionHandler` 接口只有一个方法：`rejectedExecution(Runnable r, ThreadPoolExecutor executor)`。四种内置实现：

- **`AbortPolicy`**（默认）：直接抛 `RejectedExecutionException`。适用于"任务不能丢"的场景，但会导致调用方感知到异常，需要 try-catch 包裹。
- **`CallerRunsPolicy`**：由提交任务的线程自己执行。相当于把压力反压回调用方，能让提交速度自然降下来，但调用方线程被阻塞，可能引发上游超时。
- **`DiscardPolicy`**：静默丢弃，不抛异常。适用于日志、监控等"丢了也无妨"的场景。
- **`DiscardOldestPolicy`**：丢弃队列里最老的任务，给新任务腾位置。适用于"新任务比老任务重要"的场景，比如消息系统。

### 自定义拒绝策略

生产场景四种都不够用，常见需求是"拒绝时记录+告警+降级"：

```java
public class AlertDiscardPolicy implements RejectedExecutionHandler {
    @Override
    public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
        // 1. 打告警日志
        log.error("线程池拒绝任务，队列大小={}, 活跃线程={}",
            executor.getQueue().size(), executor.getActiveCount());
        // 2. 上报监控（Prometheus counter）
        metrics.increment("thread_pool.rejected");
        // 3. 落库补偿（消息可持久化场景）
        smsRecordDao.insert(new FailedTaskRecord(r.toString(), LocalDateTime.now()));
    }
}
```

另一个场景是"有界重试"——不允许丢，但又要限流：

```java
public class BlockingRetryPolicy implements RejectedExecutionHandler {
    @Override
    public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
        if (!executor.isShutdown()) {
            try {
                // 阻塞等待，直到队列有空位
                executor.getQueue().put(r);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

### 选型原则

| 场景 | 推荐策略 |
|------|---------|
| 订单、支付（不能丢） | BlockingRetry + 落库补偿 |
| 日志上报（丢了可接受） | DiscardPolicy + 告警 |
| 调用方能接受降速 | CallerRunsPolicy |
| 新任务比老任务重要 | DiscardOldestPolicy |
| 生产通用 | 自定义：告警 + 落库 + 监控 |

## Follow-ups

- 线程池拒绝后上游怎么处理？
- `CallerRunsPolicy` 适合什么场景？
- 如何监控线程池的拒绝率？

## Related Concepts

- RejectedExecutionHandler
- Backpressure
- Thread Pool
- Metrics / Monitoring
- Circuit Breaker

## In Outlines

- [[interview/outlines/java-concurrency]]
