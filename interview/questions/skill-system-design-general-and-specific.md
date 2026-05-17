---
title: 如何设计通用技能和特定技能的 Skill 系统？
category: interview-question
topic: agent
subtopics: [skills, skill-registry, dynamic-injection, permissions, routing]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts: []
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md
source_questions:
  - 场景题：如果要你运用 skills 运用到你的项目当中，你需要怎么设计？抽象通用技能和特定技能赋予能详细展开说说么？
summary: 考察 Skill Registry、通用技能、业务特定技能、动态技能注入、权限隔离和工具上下文控制。
created: 2026-05-08T16:37:44+08:00
updated: 2026-05-08T16:37:44+08:00
---

# 如何设计通用技能和特定技能的 Skill 系统？

## Variants

- 如果要把 skills 运用到项目中，怎么设计？
- 通用技能和特定技能怎么抽象？
- 如何动态赋予 Agent 技能？

## Short Answer

Skill 系统可以设计成插件化 Skill Registry。通用技能是所有 Agent 的基础能力，如文件读写、搜索、终端、代码检索；特定技能是面向业务域的能力，如查询内部日志、触发 CI/CD、操作素材库或调用特定平台 API。运行时不应把所有技能都塞给模型，而应通过意图识别、权限判断和检索按需注入，保证能力可扩展、上下文精简、权限可控。

## Full Answer

Skill 的价值是把一类可复用能力封装起来，让 Agent 不必每次从零理解工具、流程和约束。设计上可以有一个 Skill Registry，记录技能名称、描述、触发条件、输入输出 schema、依赖工具、权限范围、示例、失败处理和审计要求。

通用技能是 Agent 的生存底座，比如文件读写、代码搜索、终端执行、Web 检索、结构化解析、测试运行。这些技能可以默认给基础 Agent，但仍然要有权限边界，比如写文件、执行命令、访问外网都要可控。

特定技能是业务能力扩展，比如查询内部日志、触发发布流水线、读取视频素材元数据、调用推荐系统实验平台。它们不应该全量常驻上下文，而应该根据用户意图、项目环境、权限和任务阶段动态注入。

动态技能赋予通常分三步。先由 router 或检索器根据请求召回候选技能；再由权限层过滤用户不能用或当前环境不安全的技能；最后只把少量相关技能的 schema、使用说明和约束放进当前上下文。这样既避免 token 爆炸，也降低模型选错工具的概率。

面试里还可以补充治理：Skill 需要版本管理、灰度、日志、评估和回滚。高风险技能要有人类确认或策略引擎拦截，技能调用结果要进入 trace，方便分析 bad case。

## Follow-ups

- Skill 和 Tool 有什么区别？
- Skill Registry 需要存哪些字段？
- 动态技能注入如何避免召回错误？
- 高风险技能如何做权限和审批？

## Related Concepts

- Skill Registry
- Dynamic Skill Injection
- Tool Schema
- Permission Boundary
- Router
- Audit Log

## In Outlines

- [[interview/outlines/agent]]
