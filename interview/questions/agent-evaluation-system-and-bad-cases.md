---
title: Agent 项目如何设计评估体系和分析 bad case？
category: interview-question
topic: evaluation
subtopics: [agent-evaluation, metrics, bad-case-analysis, offline-eval, online-eval]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - 如何做评估的体系呢，怎么评判你的效果，或者 bad case? 那最后项目的效果如何？
summary: 考察 Agent 评估体系设计，包括任务成功率、工具调用质量、过程指标、离线回归、线上指标和 bad case 闭环。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-08T16:37:44+08:00
---

# Agent 项目如何设计评估体系和分析 bad case？

## Variants

- Agent 项目如何做评估体系？
- 怎么评判效果？
- Bad case 怎么分析？
- 最后项目效果如何？

## Short Answer

Agent 评估要分层：最终结果看任务成功率、正确率、用户满意度和业务指标；过程质量看工具选择、参数正确性、步骤数、重试次数、耗时和 token 成本；安全质量看越权、幻觉、敏感操作和格式违规。方法上要结合离线评测集、线上 A/B、人工标注、日志回放和 bad case 归因，把失败沉淀回数据集、prompt、工具描述、路由策略和系统约束。

## Full Answer

Agent 评估不能只看最终回答像不像，因为 Agent 的失败可能发生在规划、检索、工具选择、参数构造、执行、反思和最终表达的任何一层。一个靠谱评估体系应该同时覆盖结果指标、过程指标、成本指标和安全指标。

结果指标回答“任务是否完成”。例如代码 Agent 可以看编译通过率、测试通过率、PR 可接受率；检索问答可以看答案正确性、引用覆盖率和用户采纳率；业务 Agent 可以看流程完成率、人工介入率和用户满意度。

过程指标回答“为什么成或败”。比如工具调用是否选对、参数是否正确、是否多次无效重试、是否陷入循环、是否检索到关键上下文、是否遵守权限和格式。过程指标很重要，因为它能把 bad case 定位到某个系统环节，而不是泛泛说“模型不行”。

评估方法上，离线要有 golden set 和回归集，覆盖高频任务、边界条件和历史 bad case；线上要看 A/B、漏斗转化、延迟、成本和人工接管率；人工评审要有 rubric，避免只凭主观印象。Bad case 分析时可以按规划失败、检索失败、工具失败、执行失败、模型幻觉、权限约束不足、评估样本缺失来归因。

最后项目效果不要只报一个数字，最好说“主指标提升 + 成本/延迟变化 + bad case 闭环”。例如任务成功率提升、人工介入率下降、平均步骤数和 token 成本可控，同时把失败样本沉淀进回归集。

## Follow-ups

- Agent 任务成功率如何定义？
- LLM-as-judge 能不能用，风险是什么？
- Bad case 如何变成回归测试？
- 如何评估工具调用质量？

## Related Concepts

- Offline Evaluation
- Online Evaluation
- Golden Set
- Regression Set
- Bad Case Analysis
- LLM-as-judge
- Tool Call Accuracy

## In Outlines

- [[interview/outlines/evaluation]]
