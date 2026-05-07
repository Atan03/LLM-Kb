---
title: TCP 如何保证可靠传输？
category: interview-question
topic: backend-system-design
subtopics: [tcp, reliability, retransmission, ack]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md
source_questions:
  - TCP 怎么保证数据包一定能到达？
summary: 考察 TCP 可靠传输机制，包括序列号、ACK、超时重传、快速重传和校验和。
created: 2026-05-06T19:40:11+08:00
updated: 2026-05-06T19:40:11+08:00
---

# TCP 如何保证可靠传输？

## Variants

- TCP 怎么保证数据包一定能到达？

## Short Answer

TCP 通过序列号、确认应答、重传和校验机制实现可靠传输。序列号保证接收方能排序和去重；ACK 告诉发送方哪些数据已收到；超时重传处理长时间未确认的数据；快速重传根据重复 ACK 提前重发丢失段；校验和用于发现传输损坏。

## Full Answer

TCP 的可靠性不是保证网络一定不丢包，而是通过协议机制在丢包、乱序和损坏发生后恢复。

序列号让每个字节流位置都有编号。接收方可以根据序列号重排乱序数据、丢弃重复数据，并知道中间缺了哪一段。确认应答让发送方知道接收方已经成功收到哪些数据。

如果发送方在超时时间内没有收到对应 ACK，就触发超时重传。为了避免每次都等超时，TCP 还有快速重传：当发送方收到多个重复 ACK，说明接收方持续等待同一段缺失数据，发送方可以提前重传。

校验和用于检测数据在传输过程中是否损坏。接收方发现校验失败会丢弃该段，后续依靠 ACK 和重传机制恢复。

面试中要避免说“TCP 保证数据包一定能到达”得太绝对。更准确的说法是：在连接有效、资源允许的前提下，TCP 通过确认、重传、排序、去重和校验，为上层提供可靠、有序、无重复的字节流抽象。

## Follow-ups

- 超时重传和快速重传有什么区别？
- TCP 如何处理乱序包？
- ACK 丢了会怎样？

## Related Concepts

- TCP
- ACK
- 序列号
- 超时重传
- 快速重传

## In Outlines

- [[interview/outlines/backend-system-design]]
