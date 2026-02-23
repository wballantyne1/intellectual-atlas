# DECISIONS

*A log of every significant decision made in this project, with reasoning. Updated after every working session.*

---

## D001 — Start with GitHub + JSON files, not a database
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** Phase 1 uses a GitHub repository with structured JSON files as the database, rather than a hosted graph database like Neo4j.

**Reasoning:** Neo4j requires infrastructure, cost, and commitment to a schema before the shape of the data is understood. JSON files in GitHub are free, version-controlled, human-readable, and easily migrated once we know what we're building. The schema will emerge from use, not be imposed in advance.

**Trade-offs accepted:** JSON is not designed for relational queries. Navigating connections across files will require scripting. This is acceptable at Phase 1 scale.

**Revisit when:** Node count exceeds ~500, or query complexity makes JSON unmanageable.

---

## D002 — Three node types: Thinkers, Ideas, Connections
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** The database has three entity types:
- **Thinkers** — individual humans (dates, geography, works, accessible summary)
- **Ideas** — concepts, arguments, movements (tradition, whether contested)
- **Connections** — relationships between any two nodes (type, weight, source citation)

**Reasoning:** This is the minimal structure that captures what we care about: not just who thought what, but how ideas relate. The connection as a first-class object (not just a link) allows us to record the *type* of relationship (influenced / contradicted / extended / responded to) and its *weight* (how significant was this connection?).

**What's not included yet:** Works (books, essays) as a separate node type. For now, works are listed as a field on Thinkers. May promote to a node type in Phase 2.

---

## D003 — Connection types: influenced, contradicted, extended, responded-to
*Date: 2026-02-23*
*Status: Provisional — expect to evolve*

**Decision:** The initial connection types are:
- `influenced` — one idea or thinker shaped another
- `contradicted` — direct opposition or refutation
- `extended` — took an idea further, built on its foundation
- `responded-to` — engaged with, whether in agreement or disagreement

**Reasoning:** These four cover most intellectual relationships. "Responded-to" is deliberately broad — it captures the act of engagement without prejudging its direction.

**Known gaps:** Doesn't yet capture "synthesised" (e.g. Kant synthesising rationalism and empiricism), "popularised", or "misread" (a historically significant misreading that became its own influence). Flag for D004+.

---

## D004 — Start with Kant and map outward
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** The first populated node is Immanuel Kant. We map backward to his influences and forward to his inheritors.

**Reasoning:** Kant is arguably the pivot of modern Western philosophy — everything before him leads toward him, everything after responds to him. Starting here gives maximum connective density from day one. It also provides a clear test of whether the data structure can handle a genuinely complex node.

**The Kant cluster (initial):**
- Backward: Hume (the man who "woke Kant from his dogmatic slumber"), Leibniz, Wolff, Newton, Rousseau
- Forward: Fichte, Schelling, Hegel (German Idealism), Schopenhauer, Nietzsche, the Neo-Kantians, early Analytic philosophy (Russell, Moore reacting against Idealism)

---

## D005 — Global scope from the outset; Western start is pragmatic not principled
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** The database is explicitly designed for global intellectual history including non-Western traditions. Starting with Kant is pragmatic (Claude's training data is deepest here), not a statement about what matters.

**Reasoning:** A database that treats Western philosophy as "philosophy" and everything else as a regional variant would be both intellectually wrong and commercially limited. The architecture must support non-Western thinkers and ideas from day one, even if population of those nodes comes later.

**Action required:** Ensure JSON schema fields are tradition-agnostic and don't embed Western defaults.

---

## D008 — Tradition as a fourth node type
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** Add Tradition as a fourth node type alongside Thinkers, Ideas, and Connections. A Tradition node represents a collective intellectual culture whose contributions cannot be attributed to named individuals.

**Reasoning:** The three original node types assumed a Western model of intellectual history where ideas are attributed to named individual thinkers. This breaks down immediately when representing Babylonian astronomy, Egyptian wisdom traditions, Vedic philosophy, Confucian schools, or any tradition that predates or operates outside the Western habit of individual attribution. Forcing these into a Thinker node (by treating "Babylonian Tradition" as a pseudo-person) would be architecturally dishonest and intellectually disrespectful. A proper Tradition node type ensures non-Western and pre-Greek intellectual traditions are first-class citizens in the database, not footnotes.

**Fields added:** region, period (start/end), key_contributions, named_thinkers (for traditions that do have some named individuals within them).

**Scope:** Applies to any collective tradition — ancient (Babylonian, Egyptian, Vedic) or ongoing (Confucian, Islamic, Indigenous). Can coexist with individual Thinker nodes from the same tradition where named individuals are known.

**Implication:** The starting cluster for the Atlas now begins with Tradition nodes (Mesopotamian, Egyptian) before arriving at the first named individual thinker (Thales).

---

## D007 — Division of labour: Claude writes files, human runs git
*Date: 2026-02-23 (revised same day)*
*Status: Confirmed*

**Decision:** Claude handles all file creation and editing only. Claude never runs git commands. At the end of every session, Claude provides an exact copy-paste block of `git add`, `git commit`, and `git push` commands. The human runs these from the Intellectual Atlas folder in their terminal.

**Reasoning (original):** Claude cannot authenticate with GitHub directly, so the human must run `git push`.

**Reasoning (revision):** Claude's sandbox shares the filesystem with the user's Mac. When Claude runs `git add`, it creates a `.git/index.lock` file that the sandbox lacks permission to delete. This leaves a stale lock that blocks the human's subsequent git operations. After repeated failures to work around this, the cleanest fix is to draw the line earlier: Claude writes files, git is entirely the human's responsibility.

**Practical implication:** Every session ends with a commit block like:
```bash
git add -A
git commit -m "description of session work"
git push
```

**Revisit when:** A sandbox environment with proper git permissions is available.

---

## D009 — Phase 1 tooling is Obsidian-native; custom tools belong to Phase 2
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** All interactive features in Phase 1 are built using Obsidian itself — native graph view, community plugins — rather than custom HTML/JS tools. Two HTML prototypes built earlier in the session (Timeline.html, Path Finder.html) are retained as Phase 2 design references but are not Phase 1 working tools.

**Phase 1 plugin stack:**
- **Dataview** — queries note frontmatter as a live database. Replaces the HTML timeline: `TABLE born_year, died_year FROM #thinker SORT born_year ASC`. Essential.
- **Path Finder** (by jerrywcy) — finds shortest paths between any two notes in the vault. Replaces the HTML path-between tool. Natively inside Obsidian.
- **Map View** — renders notes as pins on an OpenStreetMap using coordinates stored in note frontmatter. Visualises the geographic spread of the Atlas.

**Reasoning:** Building custom HTML tools in Phase 1 duplicates functionality that already exists as mature Obsidian plugins. The principle should be: use what Obsidian provides natively; build custom only in Phase 2 when the React canvas becomes the primary interface. Everything should stay within one environment without jumping to a browser.

**Frontmatter additions required:** To support Dataview and Map View, notes need numeric year fields (`born_year`, `died_year`, `first_year`) and coordinate fields (`location: [lat, lng]`) alongside the existing human-readable text fields.

**Revisit when:** Moving to Phase 2, at which point custom React tools replace all Obsidian plugins.

---

## D006 — Four living documentation files maintained after every session
*Date: 2026-02-23*
*Status: Confirmed*

**Decision:** ROADMAP.md, DECISIONS.md, CHANGELOG.md, and ARCHITECTURE.md are maintained as living documents, updated by Claude at the end of every working session.

**Reasoning:** Without documentation discipline, a project like this accumulates invisible decisions that become impossible to unpick later. The four files cover: where we're going (ROADMAP), why we made choices (DECISIONS), what changed (CHANGELOG), and how it's built (ARCHITECTURE). Together they mean the project can be resumed by any future version of Claude, or by a human collaborator, without loss of context.
