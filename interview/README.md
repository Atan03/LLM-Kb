---
title: Interview System
---

# Interview System

This layer turns the wiki knowledge graph into an interview preparation system.

## Roles

- `questions/` stores canonical interview questions after deduplication and merge.
- `outlines/` stores topic lecture notes that grow over time like mini-books.
- `_meta/` stores topic registry, merge history, and maintenance ledgers.
- `inbox/` stores proposed topics that need human confirmation.

## Relationship To The Wiki

- `concepts/`, `entities/`, `skills/`, `references/`, and `synthesis/` remain the source of truth for durable knowledge.
- Interview questions are probes into that knowledge graph.
- Outlines are a condensed teaching and revision view built from both questions and knowledge pages.

## Operating Principle

Do not create one page per raw question phrasing.
Merge near-duplicate prompts into canonical questions and grow the relevant outline.
