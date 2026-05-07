---
title: Wiki Workflow
---

# Wiki Workflow

## Architecture

- Vault: `/Users/atan/Obsidian/LLM-Kb`
- Raw sources: `/Users/atan/Documents/llm-wiki-sources`

## Source Folders

- `papers/` - papers and PDFs
- `interviews/` - interview notes and Q&A
- `blog-posts/` - articles and web clippings
- `notes/` - personal study notes
- `screenshots/` - screenshots, diagrams, whiteboards
- `benchmarks/` - experiment outputs and comparisons
- `misc/` - uncategorized material

## Workflow

1. Drop new source files into `/Users/atan/Documents/llm-wiki-sources/`
2. Ask Codex to run `wiki-ingest` to grow the knowledge graph
3. Ask Codex to run `interview-ingest` to extract, merge, enrich, and organize interview questions
4. Ask Codex to run `wiki-status` when you want the vault status
5. Ask Codex to run `outline-reframe <topic>` when an outline needs a better frame
6. Ask Codex questions with `wiki-query`

## Knowledge And Interview Layers

- `concepts/`, `entities/`, `skills/`, `references/`, `synthesis/` store durable knowledge and its relationships.
- `interview/questions/` stores canonical questions after deduplication.
- `interview/outlines/` stores lecture-style revision notes that grow from both questions and knowledge pages.
- New interview topics must be confirmed before they become permanent outlines.
- Interview inputs do not need to be complete. Missing or thin answers should be expanded by Codex using the wiki knowledge graph, related sources, and careful synthesis, then marked with an honest answer state.

## Interview Ingest Principle

- Raw interview material can be incomplete, noisy, or answerless.
- `interview-ingest` should not stop at extraction and filing.
- It should actively build the interview library by filling answer gaps, enriching weak answers, merging overlapping questions, and strengthening the associated outline.
- Synthesized answers should be useful for study, but their confidence must be represented honestly through the question page state.

## Raw Drafts

Use `_raw/` only for temporary drafts you want promoted into the wiki.
Files promoted from `_raw/` are deleted after successful processing.
Files in `/Users/atan/Documents/llm-wiki-sources/` are retained after ingest.
