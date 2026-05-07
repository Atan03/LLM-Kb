---
title: TCP 拥塞控制怎么做？
category: interview-question
topic: backend-system-design
subtopics: [tcp, congestion-control, slow-start, fast-recovery]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - TCP 拥塞控制怎么做的？
summary: 考察 TCP 拥塞窗口、慢启动、拥塞避免、快速重传和快速恢复。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# TCP 拥塞控制怎么做？

## Variants

- TCP 拥塞控制怎么做的？

## Short Answer

TCP 拥塞控制由发送方维护拥塞窗口 `cwnd`。开始时慢启动，窗口指数增长以探测带宽；达到阈值 `ssthresh` 后进入拥塞避免，窗口线性增长；收到多个重复 ACK 时触发快速重传和快速恢复，通常降低阈值和窗口后继续传输；超时重传更严重，会把窗口降回较小值重新慢启动。

## Full Answer

拥塞控制解决的是网络整体承载能力问题，不是单个接收方处理不过来的流量控制问题。发送方通过拥塞窗口 `cwnd` 控制在未确认前最多能发送多少数据。

慢启动阶段并不是真的慢，而是从较小窗口开始指数增长。每收到 ACK，窗口逐步扩大，用较快速度探测网络可用带宽。

当 `cwnd` 达到慢启动阈值 `ssthresh` 后，进入拥塞避免阶段。此时窗口改为线性增长，避免继续指数扩张把网络压垮。

当出现丢包信号时，TCP 会降低发送速率。如果收到多个重复 ACK，通常认为有个别报文段丢失但网络仍有传输能力，于是触发快速重传，并进入快速恢复，降低阈值和窗口后继续发送。如果发生超时重传，说明拥塞可能更严重，窗口会被降到很小，重新慢启动。

面试时可以补一句区分：拥塞控制面向网络，核心变量是 `cwnd`；流量控制面向接收端，核心变量是接收窗口 `rwnd`。

## Follow-ups

- 拥塞控制和流量控制有什么区别？
- 为什么超时重传比重复 ACK 更严重？
- `cwnd` 和 `rwnd` 如何共同限制发送量？

## Related Concepts

- 拥塞窗口
- 慢启动
- 拥塞避免
- 快速重传
- 快速恢复

## In Outlines

- [[interview/outlines/backend-system-design]]
