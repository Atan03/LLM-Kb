---
title: AI Coding 全栈开发的难点和流程是什么？
category: interview-question
topic: agent
subtopics: [ai-coding, repo-context, rag, self-repair, code-agent]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - 你的项目的难点在于什么地方，在 aicoding 的全栈开发的流程是怎么样的呢，你如何解决这些问题的？
summary: 考察 AI Coding Agent 如何处理代码库级上下文一致性、跨文件依赖、检索、生成、验证和自修复。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-08T16:37:44+08:00
---

# AI Coding 全栈开发的难点和流程是什么？

## Variants

- 你的项目难点在哪里？
- AI Coding 的全栈开发流程是什么？
- 如何解决跨前后端、跨文件修改时的一致性问题？

## Short Answer

AI Coding 全栈开发最大的难点不是让模型写出局部函数，而是保持代码库级上下文一致性。一个稳妥流程是：需求解析和任务拆解 -> 代码检索与依赖分析 -> 代码生成或修改 -> 编译、测试、静态检查 -> 根据报错自修复。关键工程手段包括 repo 级 RAG、AST/调用图/依赖图、变更计划、测试反馈循环和人类确认高风险修改。

## Full Answer

AI Coding 很容易在局部代码上表现不错，但在全栈项目里，真实难点往往来自跨文件、跨层级的一致性。比如改一个数据库字段，可能同时影响前端类型、接口协议、后端路由、ORM 映射、迁移脚本、测试用例和文档。模型如果只看当前文件，很容易漏改引用、误判约束或生成与项目风格不一致的代码。

流程上可以把 AI Coding Agent 设计成一个闭环。第一步是 Planner，把用户需求拆成可验证的子任务，并识别高风险区域。第二步是 Retriever，通过 repo 级 RAG、符号搜索、调用链、AST 或依赖图找出相关文件。第三步是 Coder，按计划做小步修改。第四步是 Reviewer，运行编译、测试、lint、类型检查或局部验证。第五步是 Repair，把报错和 diff 作为新上下文回到检索和修改阶段，直到通过检查或触发人工介入。

回答时要强调上下文不是越多越好，而是要“结构化地拿对上下文”。AST 和依赖图适合回答“谁引用了谁”，RAG 适合召回相似实现和规范，测试反馈适合约束模型别靠幻觉收尾。生产系统还需要限制写入范围、保留 diff、要求高风险操作确认，并把失败 case 沉淀成评估集。

## Follow-ups

- Repo 级 RAG 和普通文档 RAG 有什么不同？
- 如何避免 AI Coding 改漏跨文件引用？
- 自修复循环如何防止越改越乱？
- 如何评价 AI Coding Agent 的效果？

## Related Concepts

- Repo RAG
- AST
- 调用图
- 依赖图
- 自修复 Agent
- 测试反馈循环

## In Outlines

- [[interview/outlines/agent]]
