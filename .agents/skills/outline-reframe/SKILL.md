---
name: outline-reframe
description: >
  Reorganize a topic outline into a stronger lecture-style frame when the current structure has become
  messy, flat, repetitive, or outdated. Use this whenever the user asks to reframe an outline, rebuild
  a topic lecture, reorganize chapters, improve the teaching flow of an interview outline, or reshape a
  topic after new interview knowledge has accumulated. This skill should preserve the connection between
  outlines, canonical questions, and the underlying wiki knowledge graph, while also folding newly
  enriched answers into a stronger study narrative.
---

# Outline Reframe

This skill restructures an existing topic outline so it becomes a better revision artifact.

It is not for small incremental updates.
Use it when the current frame no longer teaches the topic well.

## Before You Start

Read:

1. The target file under `interview/outlines/`
2. `interview/_meta/topics.md`
3. `interview/_meta/outline-registry.md`
4. `interview/_meta/reframe-queue.md`
5. The linked canonical question pages under `interview/questions/`
6. The most relevant linked wiki knowledge pages under `concepts/`, `entities/`, `skills/`, `references/`, `synthesis/`

## What This Skill Optimizes For

The result should feel like:

- A compact lecture
- A revision handout
- A mini-book chapter map
- A stronger study artifact than the raw set of linked questions

It should not feel like:

- A flat tag page
- A long unordered dump of questions
- A fragile topic map with accidental chapter names

## Reframe Triggers

Reframe when you see signals like:

- Too many flat sections
- Repeated ideas across chapters
- Several adjacent questions that imply a missing chapter
- A better top-level storyline is now visible
- The outline has drifted away from the current interview market

## Reframe Procedure

### Step 1: Diagnose The Current Frame

Summarize:

- Current chapters
- What each chapter is trying to do
- Where the structure is weak
- Which question clusters are under-served
- Which concepts are carrying too much weight

### Step 2: Derive A Better Teaching Spine

Choose a stronger organizing principle such as:

- Foundation -> mechanisms -> trade-offs -> high-frequency questions -> new directions
- Problem -> solution pattern -> implementation details -> failure modes -> follow-ups
- System view -> component view -> operational view -> market trend view

Do not preserve the old chapter layout just because it already exists.

### Step 3: Reassign Questions And Concepts

For each canonical question:

- Decide which chapter teaches it best
- Keep the question link in the outline
- Group related questions into clusters

For each important concept:

- Place it where it supports the teaching flow
- Avoid scattering the same concept across many weak chapters

### Step 4: Rewrite The Outline

The reframed outline should usually contain:

- `## Why This Topic Matters`
- chapter sections with meaningful names
- question clusters under each chapter
- concept anchors under each chapter
- `## Common Confusions` or `## Emerging Directions` where relevant
- `## Open Gaps` when the topic still has thin coverage

When strong answers already exist on canonical question pages, compress and integrate their essence into the lecture flow instead of forcing the user to click every question to study the topic.

### Step 5: Preserve Traceability

Do not orphan the interview layer from the wiki.

After reframing:

- Question links must still point to canonical question pages
- Major concepts must still point to wiki knowledge pages
- The outline should still reveal what is established versus newly emerging

### Step 6: Update Ledgers

Update:

- `interview/_meta/outline-registry.md`
- `interview/_meta/reframe-queue.md`
- `log.md`
- `hot.md` if the reframing materially changed the revision surface

Example log entry:

- `[TIMESTAMP] OUTLINE_REFRAME topic="agent" previous_chapters=5 new_chapters=6 reason="new question cluster on evaluation and routing"`

## Quality Checklist

- [ ] The new frame is stronger than the old one
- [ ] Chapters are coherent and non-overlapping
- [ ] Questions are grouped by teaching logic, not accidental source order
- [ ] Concept links still tie back to the knowledge graph
- [ ] The outline reads like a lecture note rather than a directory
