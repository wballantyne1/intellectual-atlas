# ARCHITECTURE

*The technical structure of the Intellectual Atlas. Updated as the system evolves.*

*Last updated: 2026-02-23*

---

## Current state: Phase 1

**Live repository:** https://github.com/wballantyne1/intellectual-atlas (private)

A GitHub repository containing structured JSON files. No server, no database, no cost.

Think of it like a filing cabinet: each drawer is a folder (`thinkers/`, `ideas/`, `connections/`), each file is a card. The connections folder is the index — it tells you how the cards relate to each other.

**Working arrangement:** Claude creates files, stages changes, and commits locally. The human runs `git push` from the Intellectual Atlas folder to sync to GitHub.

---

## Directory structure

```
intellectual-atlas/
├── README.md
├── ROADMAP.md
├── DECISIONS.md
├── CHANGELOG.md
├── ARCHITECTURE.md
│
├── data/
│   ├── thinkers/          One .json file per thinker
│   ├── ideas/             One .json file per idea or concept
│   └── connections/       One .json file per relationship between nodes
│
└── schemas/               JSON Schema definitions (the "rules" for each file type)
    ├── thinker.schema.json
    ├── idea.schema.json
    └── connection.schema.json
```

---

## Node types

### Thinker

A human being who contributed to intellectual history.

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier, e.g. `kant-immanuel` |
| `type` | string | Always `"thinker"` |
| `name` | string | Full name |
| `born` | string | Birth year or date |
| `died` | string | Death year, date, or `"living"` |
| `nationality` | string | Country or region of origin |
| `tradition` | array | Philosophical/intellectual tradition(s) |
| `key_works` | array | Major works with year |
| `summary` | string | Accessible 2–3 sentence summary |
| `long_summary` | string | Longer scholarly summary (optional) |
| `tags` | array | Free-form tags for search/filtering |
| `related_nodes` | array | IDs of directly connected nodes (denormalised for quick lookup) |

### Idea

A concept, argument, movement, or doctrine.

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier, e.g. `transcendental-idealism` |
| `type` | string | Always `"idea"` |
| `name` | string | Name of the idea |
| `tradition` | array | Tradition(s) this idea belongs to |
| `contested` | boolean | Is this idea significantly disputed? |
| `summary` | string | Accessible 2–3 sentence summary |
| `long_summary` | string | Longer scholarly summary (optional) |
| `first_appearance` | string | Approximate date of first articulation |
| `primary_thinker` | string | ID of the thinker most associated with this idea |
| `tags` | array | Free-form tags |
| `related_nodes` | array | IDs of directly connected nodes |

### Tradition

A collective intellectual culture whose contributions cannot be attributed to named individuals.

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier in kebab-case, e.g. `babylonian-intellectual-tradition` |
| `type` | string | Always `"tradition"` |
| `name` | string | Name of the tradition |
| `region` | string | Geographic region or civilisation |
| `period.start` | string | Approximate start date, e.g. `"c. 3000 BCE"` |
| `period.end` | string | Approximate end date, or `"ongoing"` |
| `summary` | string | Accessible 2–3 sentence summary |
| `long_summary` | string | Longer scholarly summary (optional) |
| `key_contributions` | array | Major intellectual contributions |
| `named_thinkers` | array | IDs of any named thinkers within this tradition |
| `tags` | array | Free-form tags |
| `related_nodes` | array | IDs of directly connected nodes |

---

### Connection

A relationship between any two nodes (thinker→thinker, thinker→idea, idea→idea, tradition→thinker, tradition→idea).

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier, e.g. `hume-to-kant-influence` |
| `type` | string | Always `"connection"` |
| `from` | string | ID of the source node |
| `to` | string | ID of the target node |
| `relationship` | string | One of: `influenced`, `contradicted`, `extended`, `responded-to` |
| `weight` | number | Significance of this connection, 1–5 (5 = foundational) |
| `description` | string | Human-readable explanation of the connection |
| `source` | string | Citation or evidence for this connection |
| `direction` | string | `"forward"` (A→B in time) or `"bidirectional"` |

---

## ID conventions

IDs are lowercase, hyphenated, and human-readable:
- Thinkers: `lastname-firstname` → `kant-immanuel`, `hegel-georg-wilhelm-friedrich`
- Ideas: `kebab-case-concept-name` → `transcendental-idealism`, `categorical-imperative`
- Connections: `from-to-relationship` → `hume-to-kant-influence`

---

## Phase 2 migration plan (preview)

When Phase 1 data is mature, we will migrate to:

- **Neo4j** — a graph database where nodes and relationships are first-class objects. The JSON structure maps directly: Thinker/Idea nodes become Neo4j nodes; Connection files become Neo4j relationships with properties.
- **React Flow** — a JavaScript library for building interactive node-based visual interfaces. Will become the primary front-end for the Atlas.

The Phase 1 JSON schema is designed to be migration-friendly: all IDs will map cleanly to Neo4j node identifiers.

---

## Obsidian setup (Phase 1 visualisation)

**Vault location:** `Intellectual Atlas/` (the nested subfolder, created by Obsidian on setup)

Obsidian is pointed at the nested `Intellectual Atlas/` subfolder as its vault. Markdown mirror notes live in `Intellectual Atlas/notes/` — one file per node, using `[[wiki-links]]` to create the connection graph.

**How the graph works:** Obsidian scans all markdown files for `[[links]]` and draws a line between any two files that reference each other. The intelligence is in the links Claude writes, not in Obsidian itself. Obsidian's graph view shows node connections but cannot differentiate edge types (influence vs. contradiction). Node colouring by type (thinker/idea/tradition) is available via Obsidian's Groups feature.

**Mirror note conventions:**
- Full nodes: complete summaries, all connections listed with wiki-links
- Stub nodes: minimal entry marked `> Stub — full node not yet built`, with key connections listed

**Limitation:** Obsidian cannot render different connection types visually. This is a known Phase 1 constraint — React Flow in Phase 2 will display edge types (influenced/contradicted/extended) as distinct visual styles.
