---
title: Hot Cache
updated: 2026-05-11 12:00:00 CST
---

# Hot Cache

*A ~500-word semantic snapshot of recent activity. Updated after every major write operation.*

## Recent Activity

- [2026-05-11 12:00:00 CST] INTERVIEW_OUTLINES_CREATED - 创建 outlines 给 `memory-and-context`（1 个记忆题）和 `algorithms`（新 topic，暂未收录题目）。
- [2026-05-11 12:00:00 CST] INTERVIEW_INGEST - 美团 Agent 开发面经；创建了 3 个规范问题（Agent 记忆系统设计、记忆检索分层与 RAG、Agent 可观测性与 Trace），更新了上下文压缩问题（合并工具调用历史丢失的新变体），更新了 RAG 和 Evaluation 两个 outline。
- [2026-05-10 18:30:00 CST] INGEST - JavaGuide "Spring 中的设计模式详解" PDF — 创建了 5 个概念知识页（IoC 容器、AOP 代理、单例注册表、Template+Callback、事件驱动），更新了 `spring-sourcecode-design-patterns` 面试题，首次打通了面试层与知识图谱层的双向链接。

- [2026-05-09 12:30:00 CST] INTERVIEW_ANSWER_ENRICHED - rewrote Full Answers for all 6 questions from 20260509 Kuaishou round 2; transformed from outline-style answers into conversational, insight-dense narratives with real production pitfalls (compression dropping negative constraints, Spring intra-class proxy bypass, Gap Lock range explosion, decorator chain ordering semantics, Hook block/warn/human-in-the-loop tiering, long-running task node-level vs task-level recovery granularity).
- [2026-05-09 12:00:00 CST] INTERVIEW_INGEST - processed `/Users/atan/Documents/llm-wiki-sources/interviews/20260509-快手-agent开发.md`; all 6 questions mapped to existing canonical pages from earlier Kuaishou interview round; variants had identical phrasing so no new variants created; cross-linked both interview sources on each canonical page.
- [2026-05-09 11:17:19 CST] INTERVIEW_INGEST - processed `/Users/atan/Obsidian/LLM-Kb/interview/sources/20260509-快手-agent后端技术面.md`; created 6 canonical questions covering context compression triggers, long-running agent workflows, Claude Code hooks, design patterns in practice/Spring, and MySQL locking strategy; updated Agent/LLM-application/Database/Backend-system-design outlines.
- [2026-05-09 11:17:19 CST] INTERVIEW_ANSWER_ENRICHED - upgraded 6 newly generated questions from concise summaries to production-grade answers with concrete architecture decisions, failure handling, metrics, and operational trade-offs; codified this as a hard skill rule.
- [2026-05-09 10:52:46 CST] INTERVIEW_OUTLINES_CREATED - created compact outlines for `mq` and `rag`; linked 4 related question pages from pending status to active outline links.
- [2026-05-09 10:43:12 CST] INTERVIEW_INGEST - processed `/Users/atan/Documents/llm-wiki-sources/interviews/20260508-淘宝天猫-agent开发.md`; created 6 canonical questions across Java concurrency, MQ, Agent orchestration, and RAG quality/anti-poisoning; updated Agent and Java concurrency outlines.
- [2026-05-08 17:17:59 CST] INTERVIEW_OUTLINES_CREATED - created compact outlines for `evaluation`, `prompt-engineering`, and `model-serving`; linked all related new question pages.
- [2026-05-08 16:37:44 CST] INTERVIEW_INGEST - processed `/Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md`; created 8 canonical questions, updated Agent outline, and treated DeepAgents/Swarm plus local LLM deployment as date-sensitive questions with current-source checks.
- [2026-05-08 16:30:30 CST] INTERVIEW_SKILL_UPDATED - added a hard rule that time-sensitive questions about current large models, provider capabilities, benchmarks, pricing, context windows, hardware, and local LLM deployment must browse current sources before drafting or enriching interview answers.
- [2026-05-07 10:27:14 CST] INTERVIEW_OUTLINE_STYLE_REFINED - removed `Why This Topic Matters` and current-question inventory prose from outline bodies; counts stay in metadata/registries while human-facing outlines begin directly with durable study guides.
- [2026-05-07 09:59:39 CST] INTERVIEW_OUTLINES_REFRAMED - updated `interview-ingest` skill and outline template to prefer compact study-guide outlines while question volume is small; reframed all 7 current outlines to remove premature chapters.
- [2026-05-07 00:00:00 CST] INTERVIEW_OUTLINES_CREATED - created first lecture-style outlines for `agent`, `llm-application`, `redis`, `java-concurrency`, `database`, `jvm`, and `backend-system-design`; linked all 11 initial canonical questions into outlines.
- [2026-05-06 19:40:11 CST] INTERVIEW_INGEST - processed first interview source `/Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md`; created 11 canonical interview questions across Agent, LLM application, Redis, Java concurrency, database, JVM, and backend system design.
- [2026-05-03 19:53:21 CST] INIT - vault created at /Users/atan/Obsidian/LLM-Kb

## Active Threads

- Interview question bank bootstrapping from Alibaba Cloud AI Agent platform interview notes; current outlines are compact导学 notes and should only become chaptered when enough related questions accumulate.

## Key Takeaways

- Taobao/Tmall source reinforces production-system depth: fanout-fanin thread coordination, Kafka throughput/latency tradeoff, long-running task state management, three-layer multi-agent orchestration, and RAG quality guardrails (hybrid retrieval, rerank threshold, anti-poisoning).
- ByteDance/Jianying source adds stronger Agent platform depth: AI Coding repo-level consistency, LangGraph state-machine orchestration, DeepAgents/Swarm architecture, Skill Registry design, Agent evaluation, prompt layering, and local LLM deployment/SysML fundamentals.
- First interview source emphasizes practical Agent platform engineering: ReAct vs Plan-Execute-Replan, token cost reduction, Function Call/MCP/Skill layering, and Skill vs Rule boundaries.
- Backend follow-up depth remains important: Redis Hash big-key migration, ThreadLocal risks, ACID, JVM object creation and GC triggers, TCP reliability and congestion control.

## Flagged Contradictions

*None yet.*
