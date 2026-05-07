---
title: Backend System Design Interview Outline
category: interview-outline
topic: backend-system-design
summary: Lecture-style revision outline for backend fundamentals that support system design answers.
question_count: 2
chapter_count: 4
status: growing
created: 2026-05-07T00:00:00+08:00
updated: 2026-05-07T00:00:00+08:00
---

# Backend System Design Interview Outline

## Why This Topic Matters

后端系统设计不只考架构图，也会追底层协议和可靠性机制。TCP 可靠传输和拥塞控制是典型基础题，能检验你是否理解服务间通信、网络异常和吞吐控制背后的机制。

## Chapter 1. Reliable Delivery Is A Protocol Abstraction

TCP 并不是让网络不丢包，而是在丢包、乱序、重复和损坏发生后，通过序列号、ACK、重传、排序、去重和校验，为上层提供可靠有序的字节流。

### Questions

- [[interview/questions/tcp-reliable-delivery]]

### Concepts

- TCP
- Sequence Number
- ACK
- Retransmission
- Checksum

## Chapter 2. Congestion Control Protects The Network

拥塞控制面向网络承载能力。发送方维护拥塞窗口 `cwnd`，通过慢启动探测带宽，通过拥塞避免线性增长，通过快速重传和快速恢复应对部分丢包，通过超时重传处理更严重拥塞。

### Questions

- [[interview/questions/tcp-congestion-control]]

### Concepts

- Congestion Window
- Slow Start
- Congestion Avoidance
- Fast Retransmit
- Fast Recovery

## Chapter 3. Reliability Versus Flow And Congestion

可靠传输、流量控制和拥塞控制要区分：可靠传输解决数据正确到达；流量控制保护接收方，核心是 `rwnd`；拥塞控制保护网络，核心是 `cwnd`。

## Chapter 4. Interview Answer Pattern

网络题回答要避免绝对化。比如“TCP 保证包一定到达”应改成“在连接有效和资源允许的前提下，为应用提供可靠有序字节流”。这类表述更准确，也更能承接异常场景追问。

## Open Gaps

- 缺少 HTTP、HTTPS/TLS、连接池、超时重试、幂等、限流、熔断和服务治理题。
