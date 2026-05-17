---
title: TCP 三次握手为什么不能少？
category: interview-question
topic: backend-system-design
subtopics: [tcp, connection-establishment, handshake, isn]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/tcp-reliability]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 三次握手为什么要三次？
summary: 考察 TCP 连接建立原理、双方序列号同步的必要性和 SYNFlood 攻击的关联。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# TCP 三次握手为什么不能少？

## Variants

- 三次握手是为了解决什么问题？
- 为什么 TCP 建立连接需要三次而不是两次？
- 四次挥手是怎么来的？

## Short Answer

三次握手是为了让双方都确认对方的发送和接收能力正常，并同步初始序列号（ISN）。两次不够——Server 发完 SYN+ACK 后单方面认为连接建立，不知道 Client 有没有收到，造成半开连接资源浪费；四次则多余，因为第二次的 SYN+ACK 已在同一个包里合并。

## Full Answer

TCP 是一个全双工协议，所以建立连接必须同时解决两个方向的数据流确认问题。

第一次握手：Client 发送 SYN（seq=x）到 Server。Server 由此得知 Client 的发送能力正常，Server 的接收能力正常（Server 收到了数据）。

第二次握手：Server 发送 SYN+ACK（seq=y, ack=x+1）到 Client。Client 由此得知：Server 能收（收到了自己的 SYN）、Server 能发（发送了 ACK）、以及 Server 选定了自己的初始序列号 y。

第三次握手：Client 发送 ACK（ack=y+1）到 Server。Server 由此得知 Client 能收（收到了自己的 SYN+ACK），Client 确认了 Server 的 ISN。

三条消息覆盖了"Client→Server 方向"、"Server→Client 方向"、"序列号同步"三个事实缺一不可。

**为什么不能两次**：Server 发完 SYN+ACK 后单方面认为连接建立。如果 Client 没收到第二步，Server 就持有了一个永远不会被使用的"半开连接"，这是资源浪费，且给 SYNFlood 攻击留下了漏洞——攻击者大量发送 SYN，Server 分配资源响应，但永远收不到 ACK，最终耗尽连接表。

**为什么不需要四次**：第二次握手的 SYN+ACK 已经是两个信号的合并（Server 的 SYN + Server 对 Client 的 SYN 的 ACK），再拆成两次毫无必要。挥手需要四次是因为关闭方向是独立的——FIN 只关闭一方向的发送，另一方向还能收数据，所以 FIN 和 ACK 不能合并。

## Follow-ups

- 为什么挥手是四次而不是三次？
- SYNFlood 攻击的原理和防御方式？
- TCP 连接建立过程中有哪些状态转换？
- TIME_WAIT 状态的作用是什么？
- TCP 连接建立时第三次握手丢包会怎样？

## Related Concepts

- TCP
- ISN（Initial Sequence Number）
- SYN
- SYNFlood
- TCP State Machine
- Half-Open Connection

## In Outlines

- [[interview/outlines/backend-system-design]]
