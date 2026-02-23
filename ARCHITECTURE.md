# ARCHITECTURE

*The technical structure of the Intellectual Atlas. Updated as the system evolves.*

*Last updated: 2026-02-23*

---

## Current state: Phase 1

A GitHub repository containing structured JSON files. No server, no database, no cost.

Think of it like a filing cabinet: each drawer is a folder (`thinkers/`, `ideas/`, `connections/`), each file is a card. The connections folder is the index — it tells you how the cards relate to each other.

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

### Connection

A relationship between any two nodes (thinker→thinker, thinker→idea, idea→idea).

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

Obsidian can read the `data/` folder and render a graph view of the markdown links. To use:
1. Open Obsidian and point it at this repository folder
2. Enable "Graph view" in the sidebar
3. The graph will show connections between files

Note: Obsidian's graph view works best with Markdown files. For richer visualisation, consider creating a parallel `/notes/` folder with `.md` files that mirror the JSON data. This can be generated automatically from the JSON.
