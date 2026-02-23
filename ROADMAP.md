# ROADMAP

*Last updated: 2026-02-23*

---

## Vision

A navigable map of human intellectual history — thinkers, ideas, and the connections between them — structured to support:

- **An interactive visual canvas** — a zoomable, explorable map of ideas across time and geography
- **Short-form video content** — animated historical figures, voiced by AI, debating and explaining their ideas
- **AI chatbots** — Claude-powered philosopher personas that can hold conversations in character
- **A public-facing educational platform** — accessible intellectual history for a general audience

The core insight: ideas don't exist in isolation. They respond to, extend, contradict, and mutate each other across centuries and cultures. Most existing resources (books, Wikipedia) present ideas flatly. We are building the connective tissue.

---

## Phase 1 — Proof of concept
*Status: IN PROGRESS*
*Estimated duration: 0–6 months*
*Cost: Near zero*

**Goals:**
- Establish and populate the core database
- Validate the data structure before committing to a schema
- Build enough nodes to see whether the system produces genuine insight
- Maintain momentum with low overhead

**Tools:**
- GitHub repository (this repo) — home for all data and documentation
- JSON files — structured data for thinkers, ideas, and connections
- Markdown files — human-readable notes and documentation
- Obsidian — early visualisation using the graph view
- Claude — intellectual labour and database population

**Starting node:** Kant. Map outward from the *Critique of Pure Reason* (1781) — backward to his influences (Hume, Leibniz, Newton) and forward to his inheritors (Hegel, Schopenhauer, Nietzsche, the Analytic tradition).

**Phase 1 is complete when:**
- ~100+ nodes exist (thinkers + ideas + connections)
- The shape of the data is well understood
- At least one visualisation (Obsidian graph) produces genuine intellectual insight
- A clear migration path to Phase 2 has been designed

---

## Phase 2 — Infrastructure
*Status: NOT STARTED*
*Estimated start: 6–18 months in*

**Goals:**
- Migrate JSON data to Neo4j (graph database — designed precisely for this kind of connected data)
- Build a custom visual canvas using React Flow
- Begin user-facing product development

**Why Neo4j:** A graph database stores relationships as first-class objects, not afterthoughts. Querying "all ideas that contradicted Kant and were later extended by Nietzsche" becomes trivial.

**Why React Flow:** A flexible, open-source library for building interactive node-based UIs. Gives full control over the visual layer.

---

## Phase 3 — Product
*Status: NOT STARTED*
*Estimated start: 18+ months in*

- Public-facing educational platform
- Video content pipeline
- AI philosopher chatbots
- Potential monetisation and institutional partnerships

---

## Decisions deferred

- Exact schema for contested vs. consensus connections (see DECISIONS.md)
- How to handle thinkers who span multiple traditions
- Whether to include living thinkers in Phase 1
