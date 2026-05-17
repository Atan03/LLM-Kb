---
title: Evaluation Interview Outline
category: interview-outline
topic: evaluation
summary: Compact study guide for evaluation design, bad-case analysis, and iteration loops in Agent systems.
question_count: 2
chapter_count: 0
status: growing
created: 2026-05-08T17:05:00+08:00
updated: 2026-05-11T12:00:00+08:00
---

# Evaluation Interview Outline

## Study Guide

评估题的核心不是报一个“准确率”数字，而是说明你如何把 Agent 的结果质量、过程质量、成本和安全放进同一个闭环。面试官通常想听到的是：你怎么定义成功、怎么定位失败、怎么把 bad case 变成下一轮改进输入。

可以按四层回答。结果层看任务完成率、正确率、用户采纳和业务指标；过程层看工具调用正确性、重试次数、循环风险、检索命中和步骤质量；成本层看 token、延迟和资源消耗；安全层看越权、幻觉、格式违规和风险操作拦截。只讲结果不讲过程，往往无法解释“为什么变好或变坏”。

## Interview Thread

建议先用 `[[interview/questions/agent-evaluation-system-and-bad-cases]]` 建立评估框架，再补“离线回归集 + 线上 A/B + 人工 rubric + bad case 归因”的闭环。新补充 `[[interview/questions/agent-observability-trace]]`，把 Agent 系统的可观测性（Trace-Span 模型、结构化日志、跨 Session 审计）纳入评估体系。

## Questions

- [[interview/questions/agent-evaluation-system-and-bad-cases]]
- [[interview/questions/agent-observability-trace]]

## Key Concepts

- Offline Eval
- Online Eval
- Golden Set
- Regression Set
- Bad Case Analysis
- Tool Call Quality
- Cost/Latency Trade-off

## Open Gaps

- 还缺少自动评审稳定性、评估偏差、线上告警阈值和评测平台化建设的面经样本。
