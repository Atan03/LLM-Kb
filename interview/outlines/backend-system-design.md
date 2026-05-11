---
title: Backend System Design Interview Outline
category: interview-outline
topic: backend-system-design
summary: Compact study guide for backend communication fundamentals and framework design-pattern understanding.
question_count: 9
chapter_count: 0
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# Backend System Design Interview Outline

## Study Guide

TCP 题不是孤立的网络八股，而是在问你是否理解服务间通信为什么“看起来可靠”，以及网络变差时发送方如何收敛流量。

可靠传输的核心不是“网络一定不丢包”，而是 TCP 在丢包、乱序、重复和损坏发生后，用协议机制给应用层提供可靠、有序、无重复的字节流。序列号负责排序和去重，ACK 负责确认接收进度，超时重传和快速重传负责恢复丢失数据，校验和负责发现损坏。

拥塞控制回答的是另一个问题：如果网络本身开始堵，发送方如何避免继续把网络打爆。发送方维护拥塞窗口 `cwnd`，慢启动用指数增长探测带宽，达到阈值后用拥塞避免线性增长；出现重复 ACK 时快速重传和快速恢复，超时重传时窗口降得更狠，再重新慢启动。

沿着通信可靠性主线展开，TCP 的初始故事就是：可靠传输保证单条连接向上提供正确字节流，拥塞控制保证发送方根据网络状态调整发送速率。面试时还要顺手区分流量控制：流量控制保护接收方，核心是 `rwnd`；拥塞控制保护网络，核心是 `cwnd`。

另一条常见后端主线是“框架源码中的设计模式落地”。装饰器强调组合增强与可插拔扩展，Spring 源码则把工厂、代理、模板方法、观察者模式用于 IOC、AOP、模板化访问和事件解耦。能把模式和真实框架行为对应起来，通常比背定义更有说服力。

## Interview Thread

建议按这个顺序复习：

- [[interview/questions/tcp-reliable-delivery]]：先理解 TCP 如何恢复丢包、乱序和损坏。
- [[interview/questions/tcp-congestion-control]]：再理解 TCP 如何根据网络拥塞调节发送窗口。
- [[interview/questions/decorator-pattern-and-practical-usage]]：补齐装饰器模式在工程中的增强链路设计。
- [[interview/questions/spring-sourcecode-design-patterns]]：补齐 Spring 源码里的模式应用与常见坑位。

这条复习线可以支撑后续服务调用、超时重试、连接池、限流熔断和网络故障排查的底层解释。

## Questions

- [[interview/questions/tcp-reliable-delivery]]
- [[interview/questions/tcp-congestion-control]]
- [[interview/questions/tcp-three-way-handshake]]
- [[interview/questions/osi-seven-layer-model]]
- [[interview/questions/decorator-pattern-and-practical-usage]]
- [[interview/questions/spring-sourcecode-design-patterns]]
- [[interview/questions/java-string-immutability-and-pool]]
- [[interview/questions/spring-annotation-implementation]]
- [[interview/questions/spring-bean-construction-requirements]]

## Key Concepts

- TCP
- Sequence Number
- ACK
- Retransmission
- Checksum
- `cwnd`
- `rwnd`
- Slow Start
- Fast Recovery
- OSI Model
- Decorator Pattern
- Factory/Proxy/Template/Observer in Spring
- String Pool / Compact Strings
- AOP / Annotation Processing
- Constructor Injection

## Open Gaps

- 还缺少 HTTP、HTTPS/TLS、连接池、超时重试、幂等、限流、熔断和服务治理题。
