# Changelog

All notable changes to the **Workflow Design Bible** are documented here.
This project follows [Semantic Versioning](https://semver.org/).

The skill version is mirrored in three places that must stay in sync:
`VERSION`, the `version:` field in `SKILL.md` frontmatter, and the top entry here.

## [2.0.0] — 2026-06-26

Structural evolution: from "the constitution *is* CLAUDE.md" to "CLAUDE.md is a thin
router over a named document system." Breaking change for the generator's output
shape; new projects should generate on v2.

### Added
- **The document system** — `CLAUDE.md` becomes a *thin boot router* (resident every
  turn) pointing to a fixed set of named single-source docs under `documentation/`,
  each loaded **once per session, not every turn**: `CONSTITUTION.md`,
  `INITIALIZATION.md`, `WORKFLOW.md`, `ROLES.md`, `IDENTITY.md`, `SOUL.md`,
  `MEMORY.md`, `NEXT_SESSION.md`, `CHANGELOG.md`, `STRUCTURE.json`,
  `configuration.json`, `playbooks/`. One template skeleton per doc under
  `templates/documentation/`.
- **Session lifecycle** — four generated skills under `templates/skills/`:
  `/start-session` (force-load the boot set, scan `reports/`, brief + begin),
  `/finalize-session` (reflect → grow living docs → rewrite `NEXT_SESSION.md` whole →
  re-condense `CHANGELOG.md` → run `doctor`), `/self-reflection` (deep architecture
  audit), `/self-reflection-cli` (execution→CLI downleveling audit).
- **Tiered boot read** — `/start-session` force-reads a *boot set* (CONSTITUTION,
  IDENTITY, SOUL, WORKFLOW, ROLES, NEXT_SESSION, MEMORY); INITIALIZATION/CHANGELOG/
  STRUCTURE/configuration/playbooks stay on-demand. Keeps session-start light.
- **Identity & soul (Philosophy 9)** — `IDENTITY.md` (factual passport) + `SOUL.md`
  (character that grows a little each finalize). The agent becomes a work partner with
  continuity of self, not a stateless tool.
- **`STRUCTURE.json`** — machine-readable manifest that `doctor` validates against the
  filesystem (constitution-as-code now has an explicit data source).
- **CHANGELOG re-condense rule** and **NEXT_SESSION rewrite-whole rule** — both docs
  stay short forever; no garbage accumulation.
- **Optional vector memory** — `MEMORY.md` documents a two-tier scheme: lightweight
  always-loaded facts + an optional ChromaDB vector store (OpenRouter embeddings) with
  a memory CLI/skill, scaffolded but off until a key is provided.

### Changed
- **Eight → nine philosophies.** Philosophy 4 is now "the document system";
  Philosophy 6 now wraps reflection in the session lifecycle; Philosophy 9 (identity +
  soul) is new.
- `templates/CLAUDE.md.template` rewritten from a full constitution into a thin router.
- Interview protocol gains Round 2 (identity & soul) and a memory/vector-DB question.

## [1.0.0] — 2026-06-18

First public release.

### Added
- **`SKILL.md`** — the installable meta system prompt: an interview-driven
  generator that scaffolds a complete "constitution + documentation system"
  for a new autonomous, agent-run project.
- **Eight design philosophies** — the reusable DNA every generated project
  constitution must embody (CEO model, all-sub-agent + concurrency, four-layer
  architecture, single-source-of-truth pointers, global/local capability tiers,
  reflection as the last step, constitution-as-code self-check, standard shape).
- **`templates/CLAUDE.md.template`** — the fill-in constitution skeleton, now
  with a **Rules / Don'ts** split (hard prohibitions vs discouraged actions).
- **`templates/configuration.json.template`** — the brand single-source-of-truth.
- Interview protocol that **auto-surveys the host environment** for reusable
  global skills/agents/MCP instead of asking the user to enumerate them.
- Self-consistency (`doctor`) pattern, dry-run/test-safety rule, locked
  strategic-decisions table, and session-handoff loop baked into the templates.
