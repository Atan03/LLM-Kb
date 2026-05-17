---
title: Interview Source Ingest Index
updated: 2026-05-12 00:00:00 CST
---

# Interview Source Ingest Index

Canonical index for source-file ingest status.
Check this file first instead of scanning long logs.

## Format

- `source`: absolute source path
- `status`: `ingested | pending | partial`
- `first_ingested`
- `last_ingested`
- `last_mode`: `created | updated | mixed`
- `canonical_created`
- `canonical_updated`
- `notes`

## Entries

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260506-阿里云-agent开发.md`
  status: `ingested`
  first_ingested: `2026-05-06 19:40:11 CST`
  last_ingested: `2026-05-06 19:40:11 CST`
  last_mode: `created`
  canonical_created: `11`
  canonical_updated: `0`
  notes: `initial interview bootstrap source`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260508-字节剪映-agent开发.md`
  status: `ingested`
  first_ingested: `2026-05-08 16:37:44 CST`
  last_ingested: `2026-05-08 16:37:44 CST`
  last_mode: `created`
  canonical_created: `8`
  canonical_updated: `0`
  notes: `includes time-sensitive model/deployment questions`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260508-淘宝天猫-agent开发.md`
  status: `ingested`
  first_ingested: `2026-05-09 10:43:12 CST`
  last_ingested: `2026-05-09 10:43:12 CST`
  last_mode: `created`
  canonical_created: `6`
  canonical_updated: `0`
  notes: `mq/rag/java-concurrency/agent depth`

- source: `/Users/atan/Obsidian/LLM-Kb/interview/sources/20260509-快手-agent后端技术面.md`
  status: `ingested`
  first_ingested: `2026-05-09 11:17:19 CST`
  last_ingested: `2026-05-09 11:17:19 CST`
  last_mode: `created`
  canonical_created: `6`
  canonical_updated: `0`
  notes: `chat-pasted source snapshot`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260509-快手-agent开发.md`
  status: `ingested`
  first_ingested: `2026-05-09 12:00:00 CST`
  last_ingested: `2026-05-09 12:00:00 CST`
  last_mode: `updated`
  canonical_created: `0`
  canonical_updated: `6`
  notes: `same question set as snapshot source, merged into existing canonical pages`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260511-快手aiagent-agent开发.md`
  status: `ingested`
  first_ingested: `2026-05-11 15:07:11 CST`
  last_ingested: `2026-05-11 15:07:11 CST`
  last_mode: `mixed`
  canonical_created: `2`
  canonical_updated: `1`
  notes: `expanded RAG pipeline coverage and added memory/function-calling orchestration questions`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260511-美团-agent开发.md`
  status: `ingested`
  first_ingested: `2026-05-11 12:00:00 CST`
  last_ingested: `2026-05-11 12:00:00 CST`
  last_mode: `mixed`
  canonical_created: `3`
  canonical_updated: `1`
  notes: `memory system design, memory retrieval RAG, agent observability trace, and context compression tool-call history enrichment`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md`
  status: `ingested`
  first_ingested: `2026-05-11 17:00:00 CST`
  last_ingested: `2026-05-11 17:00:00 CST`
  last_mode: `mixed`
  canonical_created: `7`
  canonical_updated: `3`
  notes: `TCP handshake, OSI model, String/StringBuffer/StringBuilder, lock types, AQS, CAS, Spring annotation, Spring bean construction, MCP/Agent implementation`

- source: `/Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md`
  status: `ingested`
  first_ingested: `2026-05-12 00:00:00 CST`
  last_ingested: `2026-05-12 00:00:00 CST`
  last_mode: `mixed`
  canonical_created: `11`
  canonical_updated: `5`
  notes: `thread pool rejection/sizing/dynamic-tuning, MySQL deep pagination/slow-SQL, JVM OOM, CoT/ToT, coreference resolution/intent, RAG chunking/evaluation/KB strategy, LoRA fine-tuning, Kafka idempotency/backpressure/backlog handling`
