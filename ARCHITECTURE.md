# ARCHITECTURE

*The technical structure of the Intellectual Atlas. Updated as the system evolves.*

*Last updated: 2026-02-23*

---

## Current state: Phase 1

**Live repository:** https://github.com/wballantyne1/intellectual-atlas (private)

A GitHub repository containing structured JSON files. No server, no database, no cost.

Think of it like a filing cabinet: each drawer is a folder (`thinkers/`, `ideas/`, `connections/`), each file is a card. The connections folder is the index — it tells you how the cards relate to each other.

**Working arrangement:** Claude creates and edits files only. Claude never runs git commands. At the end of each session the human runs the commit block Claude provides. See D007.

---

## End-of-session git workflow

At the end of every working session, run these three commands from the `Intellectual Atlas` folder:

```bash
git add -A
git commit -m "brief description of what was done"
git push
```

`git add -A` stages everything — new files, edits, and deletions. This is intentional: Claude manages the files, so whatever is in the folder is what should be committed.

**Why Claude doesn't run git:** Claude's sandbox shares the filesystem with your Mac but cannot clean up git lock files it creates, which blocks subsequent git operations. Running git exclusively from the Mac terminal avoids this entirely.

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

**How the graph works:** Obsidian scans all markdown files for `[[links]]` and draws a line between any two files that reference each other. The intelligence is in the links Claude writes, not in Obsidian itself. Node colouring by type (thinker/idea/tradition) is available via Obsidian's Groups feature.

**Mirror note conventions:**
- Full nodes: complete summaries, all connections listed with wiki-links
- Stub nodes: minimal entry marked `> Stub — full node not yet built`, with key connections listed

---

## Phase 1 plugin stack

All interactive features are Obsidian-native. No browser required. Install via Settings → Community plugins → Browse.

| Plugin | Purpose | Status |
|--------|---------|--------|
| **Dataview** | Live queries on note frontmatter — timeline views, filtered lists, sorted tables | Install |
| **Path Finder** (by jerrywcy) | Finds shortest path between any two notes in the vault | Install |
| **Map View** | Renders notes as pins on OpenStreetMap using `location` frontmatter coordinates | Install |

### Dataview queries (examples)

Timeline of thinkers sorted by birth year:
```dataview
TABLE born_year, died_year, nationality FROM #thinker
WHERE born_year != null
SORT born_year ASC
```

All ideas before 0 CE:
```dataview
TABLE first_year, primary_thinker FROM #idea
WHERE first_year < 0
SORT first_year ASC
```

All nodes in a given tradition:
```dataview
LIST FROM #thinker OR #idea
WHERE contains(tradition, "Stoicism")
```

### Note frontmatter fields (extended for plugins)

Thinker notes carry the following YAML frontmatter:
```yaml
tags: [thinker]
born_year: -624        # integer; negative = BCE. Used by Dataview.
died_year: -546        # integer. Used by Dataview.
location: [37.53, 27.28]   # [lat, lng]. Used by Map View.
```

Idea notes:
```yaml
tags: [idea]
first_year: -585       # integer; year of first articulation. Used by Dataview.
```

Tradition notes:
```yaml
tags: [tradition]
start_year: -3000      # integer. Used by Dataview.
end_year: -500         # integer. Used by Dataview.
location: [32.54, 44.42]   # [lat, lng]. Used by Map View.
```

---

**Known limitation:** Obsidian cannot render different connection types visually (influence vs. contradiction appears as identical edges). This is a Phase 1 constraint — React Flow in Phase 2 will display edge types as distinct visual styles.
