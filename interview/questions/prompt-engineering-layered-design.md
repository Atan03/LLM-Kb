---
title: Prompt Engineering 如何分层设计以减少问题？
category: interview-question
topic: prompt-engineering
subtopics: [prompt-layering, modular-prompt, context, tools, few-shot]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - PE 你要如何分层设计会减少问题呢？
summary: 考察提示词模块化、职责分层、上下文隔离、工具约束和可维护性。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-08T16:37:44+08:00
---

# Prompt Engineering 如何分层设计以减少问题？

## Variants

- PE 如何分层设计？
- 如何减少 prompt 维护问题？
- Prompt 里 persona、context、tools、examples 应该如何组织？

## Short Answer

Prompt 不应该把所有指令揉在一起，而应模块化分层。常见顺序是：全局角色和不可违反规则、任务目标、上下文和检索材料、工具/Skill 使用约束、输出格式、few-shot 示例、用户输入。这样能降低指令冲突，方便定位问题：语气错改 persona，工具错改 tool schema，格式错改 output contract，上下文错改检索和压缩策略。

## Full Answer

提示词分层的核心是职责单一。最内层是全局身份和硬约束，例如安全边界、禁止行为、必须确认的高风险动作。第二层是任务目标和成功标准，让模型知道当前要优化什么。第三层是上下文，包括用户状态、业务变量、RAG 片段、历史摘要和当前环境。第四层是工具或 Skill 约束，包括可用工具、参数 schema、调用条件和错误处理。第五层是输出格式和 few-shot 示例，约束模型怎么表达。最后才是用户本次输入。

这样设计的好处是可维护。模型总是调错接口时，优先检查工具描述和 schema；模型输出格式不稳定时，检查 output contract 和 examples；模型答非所问时，检查任务目标和上下文召回；模型越权时，检查全局规则和运行时权限。

还要注意 prompt 分层不等于越长越好。稳定规则可以放 system prompt，动态上下文应按需注入，工具说明应按任务裁剪，few-shot 要少而准。对于 Agent 系统，prompt 分层还要配合 trace 和 eval，否则很难判断是哪一层导致 bad case。

## Follow-ups

- System prompt 和 user prompt 分别适合放什么？
- Few-shot 太多会有什么问题？
- Prompt 分层如何和工具权限配合？
- 如何 debug prompt 层之间的冲突？

## Related Concepts

- Modular Prompt
- Persona
- Context
- Tool Constraint
- Output Contract
- Few-shot

## In Outlines

- [[interview/outlines/prompt-engineering]]
