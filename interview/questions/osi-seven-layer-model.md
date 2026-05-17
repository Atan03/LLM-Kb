---
title: OSI 七层模型每层有哪些代表协议和设备？
category: interview-question
topic: backend-system-design
subtopics: [osi-model, network-layer, tcp-ip, protocol]
question_type: traditional
answer_status: reviewed
priority: medium
frequency: medium
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - OSI 七层模型及相关协议分别属于哪一层？
summary: 考察网络分层思维、常见协议归属、以及 TCP/IP 模型对 OSI 的简化。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# OSI 七层模型每层有哪些代表协议和设备？

## Variants

- OSI 七层模型是哪七层？
- 常见协议分别属于哪一层？
- TCP/IP 四层模型是什么？

## Short Answer

OSI 七层从下到上：物理层（网线、光纤）→ 数据链路层（MAC、交换机）→ 网络层（IP、路由器）→ 传输层（TCP/UDP）→ 会话层（TLS/SSL 握手）→ 表示层（编码、加密）→ 应用层（HTTP/DNS/FTP）。TCP/IP 四层模型是其简化版，合并了物理+链路层、合并了会话+表示层。

## Full Answer

OSI 七层模型是理解计算机网络的理论基础，虽然实际互联网主要基于 TCP/IP 协议栈，但分层思维在排查网络问题、设计系统通信时非常关键。

**第 1 层 — 物理层**：管的是物理介质和电信号转换。网线（双绞线）、光纤（光信号）、Hub（集线器）都在这层工作。物理层只传输原始比特流，不理解任何协议。

**第 2 层 — 数据链路层**：在相邻节点间传输帧，以 MAC 地址标识节点。代表协议：Ethernet（以太网帧）、MAC 地址、ARP（IP→MAC 映射）。代表设备：交换机（Switch）。交换机根据 MAC 地址表做帧转发，学习和泛洪是这个层的核心行为。

**第 3 层 — 网络层**：端到端的路由选择，核心是 IP 协议。代表协议：IP、ICMP（Ping/Traceroute）、OSPF（动态路由）、路由器在这一层工作。路由器根据目的 IP 地址查路由表做转发决策。

**第 4 层 — 传输层**：提供端到端的进程级可靠性保证。TCP（面向连接、可靠、有序）vs UDP（无连接、不可靠但低延迟）。三次握手、四次挥手、流量控制、拥塞控制都是这层的核心机制。

**第 5 层 — 会话层**：管理通信会话的建立、维护和终止。TLS/SSL 握手过程跨越了会话层和表示层，NetBIOS 也是这层的代表。

**第 6 层 — 表示层**：负责数据格式转换、编码和压缩。JPEG、ASCII、加密/解密（SSL/TLS 的加密部分）属于这一层。

**第 7 层 — 应用层**：直接为用户或应用程序提供服务的协议层。HTTP、HTTPS、DNS、FTP、SMTP、WebSocket 全在这里。

TCP/IP 四层模型（DoD 模型）是实际使用的主流：网络接口层（合并物理+链路）、网络层（IP）、传输层（TCP/UDP）、应用层（合并会话+表示+应用）。

一个常见面试延伸是：协议不一定严格属于某一层。HTTP/3 基于 QUIC（UDP），QUIC 既是传输层（可靠 UDP）也是应用层（多路复用、拥塞控制）。这种"协议跨层"现象正好体现了 OSI 模型的局限性，实际网络远比层级模型复杂。

## Follow-ups

- 交换机和路由器的区别是什么？
- 为什么有了 TCP/IP 还要学 OSI 七层？
- HTTP/3 为什么用 QUIC（UDP）而不是 TCP？
- ARP 协议的工作流程？

## Related Concepts

- OSI Model
- TCP/IP Model
- Network Layer
- Switch vs Router
- Protocol Stack
- HTTP/3 + QUIC

## In Outlines

- [[interview/outlines/backend-system-design]]
