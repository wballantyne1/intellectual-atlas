# The Intellectual Atlas

> *Human intellectual history, mapped as a navigable, living system.*

The Intellectual Atlas is a structured database of intellectual history — thinkers, ideas, and the connections between them — designed to support rich visualisation, AI interaction, and multi-format content production.

## What this is

Books are linear. Wikipedia is flat and decontextualised. The Intellectual Atlas treats the history of ideas as geography: with routes, mutations, and contested territories. Ideas don't just influence each other — they contradict, extend, and respond to each other. Disagreement is mapped as richly as influence.

## Project status

**Phase 1** — Proof of concept. Building and populating the database. Starting node: Kant.

See `ROADMAP.md` for the full vision and current status.

## Repository structure

```
data/
  thinkers/     — JSON files, one per thinker
  ideas/        — JSON files, one per idea/concept
  connections/  — JSON files describing relationships between nodes
schemas/        — JSON schemas defining the data structure
ROADMAP.md      — Vision, phases, current status
DECISIONS.md    — Every significant decision and its reasoning
CHANGELOG.md    — Dated log of every change
ARCHITECTURE.md — Technical structure as it evolves
```

## Roles

- **Human**: Creative director and editor — sets direction, reviews output, makes judgment calls on contested connections
- **Claude**: Intellectual labour, research, writing, and maintaining the system
