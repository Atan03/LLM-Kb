---
title: SFT 和 RL 在大模型训练中有什么区别？
category: interview-question
topic: model-serving
subtopics: [sft, reinforcement-learning, post-training, ppo, reward]
question_type: emerging
answer_status: reviewed
priority: medium
frequency: medium
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - 有做过本地部署模型训练么，强化学习和监督微调了解过么？
  - 样本量极少的情况下，如何解决 LoRA 微调容易出现的过拟合或欠拟合问题？
summary: 考察 SFT 与 RL 的训练信号、目标、数据形态、适用场景和工程风险，以及少样本 LoRA 的过拟合/欠拟合处理。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-12T00:00:00+08:00
---

# SFT 和 RL 在大模型训练中有什么区别？

## Variants

- 强化学习和监督微调了解过么？
- SFT 和 RL 有什么区别？
- 大模型后训练为什么要先 SFT 再 RL？

## Short Answer

SFT 是监督学习，核心是用高质量指令-答案数据做行为克隆，让模型学会“好答案长什么样”。RL 是基于奖励信号优化策略，让模型在生成过程中探索更高奖励的行为，适合优化偏好、推理过程、工具使用或代码运行结果等难以直接用标准答案覆盖的目标。SFT 更稳定、数据要求清晰；RL 上限更高，但奖励设计、训练稳定性和成本更难。

## Full Answer

SFT 的训练信号来自人工或合成的标准答案。给定输入，模型学习模仿目标输出，因此它适合让模型掌握格式、语气、领域知识、工具调用样式和基础任务行为。它的优点是工程上相对稳定，评估也直接；缺点是模型主要学习数据分布，遇到需要探索或长期目标优化的任务时，上限受示范数据限制。

RL 的训练信号来自 reward。模型生成多个候选行为后，根据奖励模型、规则校验、环境反馈或任务结果打分，再优化策略。它适合偏好对齐、复杂推理、代码执行成功率、工具链任务成功率等目标。比如代码任务可以把编译通过、测试通过作为奖励的一部分。

回答时可以用“先教会，再优化”来串：SFT 先把模型拉到能正常回答、遵守格式和理解任务的区域；RL 再围绕更难人工显式标注的目标做优化。风险也要说清楚：RL 的 reward 如果设计不好，会出现 reward hacking、训练不稳定、成本高和泛化差。

## Follow-ups

- SFT 数据怎么构造？
- RLHF、RLAIF、PPO、GRPO 有什么关系？
- 为什么 RL 容易 reward hacking？
- 代码 Agent 的 reward 可以怎么设计？

## Related Concepts

- SFT
- RLHF
- Reward Model
- PPO
- GRPO
- Reward Hacking

## In Outlines

- [[interview/outlines/model-serving]]
