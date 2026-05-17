---
title: Prompt Engineering Interview Outline
category: interview-outline
topic: prompt-engineering
summary: Compact study guide for modular prompt layering, maintainability, and debugging.
question_count: 1
chapter_count: 0
status: growing
created: 2026-05-08T17:05:00+08:00
updated: 2026-05-08T17:05:00+08:00
---

# Prompt Engineering Interview Outline

## Study Guide

提示词题的重点不在“会写一段漂亮 prompt”，而在“如何把提示词当作可维护系统来设计”。最稳妥的表达是分层职责：全局规则、任务目标、上下文、工具约束、输出契约、few-shot、用户输入。每层解决不同问题，避免指令互相污染。

面试里最好强调可维护性：语气错改 persona，格式错改 output contract，接口错改 tool schema，答非所问先查目标和上下文召回。这样回答能体现你在做系统调试，而不是只做文案调参。

## Interview Thread

先用 `[[interview/questions/prompt-engineering-layered-design]]` 把层次讲清，再补一句“分层不是越长越好，动态上下文和工具说明要按需注入”。最后可以接到评估：分层调整要看 trace 和 bad case，而不是只靠主观感觉。

## Questions

- [[interview/questions/prompt-engineering-layered-design]]

## Key Concepts

- Modular Prompt
- Persona
- Task Contract
- Context Injection
- Tool Constraint
- Output Schema
- Few-shot

## Open Gaps

- 还缺少跨模型迁移、长上下文压缩策略、提示词版本治理和自动优化策略的面经样本。
