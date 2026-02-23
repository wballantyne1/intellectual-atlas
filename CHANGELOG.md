# CHANGELOG

*A dated log of every change made to this repository. Most recent first.*

---

## 2026-02-23 — Session 1 (continued): Ancient world cluster and Tradition node type

**Schema:**
- `schemas/tradition.schema.json` — fourth node type for collective intellectual traditions

**Data — Traditions (new folder `data/traditions/`):**
- `babylonian-intellectual-tradition.json`
- `egyptian-wisdom-tradition.json`

**Data — Thinkers:**
- `thales-of-miletus.json` — first named thinker in Western philosophy

**Data — Ideas:**
- `naturalistic-explanation.json` — the founding gesture of Western philosophy
- `arche.json` — the Pre-Socratic search for a first principle
- `maat-concept.json` — Egyptian cosmic order, structural precursor to Logos

**Data — Connections:**
- `babylonian-to-thales-influence.json`
- `egyptian-to-thales-influence.json`

**Obsidian notes (new, in `Intellectual Atlas/notes/`):**
- Full notes: Babylonian Intellectual Tradition, Egyptian Wisdom Tradition, Thales of Miletus, Arche, Ma'at, Naturalistic Explanation
- Stubs: Anaximander of Miletus, Heraclitus of Ephesus, Pythagoras of Samos, Democritus of Abdera

**Documentation updated:** DECISIONS.md (D008), ARCHITECTURE.md (Tradition node type, Obsidian setup)

**Direction change:** Starting cluster moved from Kant to the roots of Western intellectual history, beginning with Near Eastern traditions (Babylonian, Egyptian) before the first named Greek thinker (Thales).

**Next session:** Full nodes for Anaximander, Heraclitus, Pythagoras, Democritus; first contradiction connection (Parmenides vs. Heraclitus on change).

---

## 2026-02-23 — Session 1 (continued): GitHub setup and remote push

**Actions:**
- Installed Homebrew (Mac package manager) and GitHub CLI (`gh`) on the human's machine
- Authenticated GitHub CLI with account `wballantyne1`
- Created private GitHub repository: `https://github.com/wballantyne1/intellectual-atlas`
- Pushed all 21 files from initial commit to remote origin

**Decisions made this session:** D007 (see DECISIONS.md)

**Workflow established:** Claude handles all file creation, editing, and git commits. Human runs `git push` from the Intellectual Atlas folder to sync to GitHub at the end of a session.

---

## 2026-02-23 — Session 1: Repository initialisation

**Added:**
- `README.md` — project overview and repository structure guide
- `ROADMAP.md` — full vision, Phase 1/2/3 breakdown, current status
- `DECISIONS.md` — D001 through D006, covering all foundational decisions
- `CHANGELOG.md` — this file
- `ARCHITECTURE.md` — technical structure, schema definitions, field reference
- `schemas/thinker.schema.json` — JSON schema for Thinker nodes
- `schemas/idea.schema.json` — JSON schema for Idea nodes
- `schemas/connection.schema.json` — JSON schema for Connection nodes
- `data/thinkers/kant-immanuel.json` — first Thinker node
- `data/ideas/transcendental-idealism.json` — first Idea node
- `data/ideas/critique-of-pure-reason.json` — second Idea node (the work as an idea-node)
- `data/connections/hume-to-kant-influence.json` — Hume's influence on Kant ("dogmatic slumber")
- `data/connections/kant-to-hegel-influence.json` — Kant's influence on Hegel

**Decisions made this session:** D001–D006 (see DECISIONS.md)

**Next session:** Populate the Kant cluster — add Hume, Leibniz, Hegel, Schopenhauer as Thinker nodes, and expand the connection graph.
