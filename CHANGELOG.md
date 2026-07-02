# Changelog

All notable changes to the **Workflow Design Bible** are documented here.
This project follows [Semantic Versioning](https://semver.org/).

The skill version is mirrored in three places that must stay in sync:
`VERSION`, the `version:` field in `SKILL.md` frontmatter, and the top entry here.

## [2.1.0] — 2026-07-02

The org-model sharpening: internal staff vs external contractors, invocation modes,
and the deterministic ground floor. No breaking changes to v2's document shape.

### Added
- **External contract partners (Philosophy 2)** — a new roster category for agents
  *outside* the runtime that the CEO cannot dispatch natively (another vendor's
  coding agent, an image-generation agent, …). Engaged by **contract, not prompt**:
  write a contract file → wake the partner via its protocol → await a completion
  report → CEO reviews. `ROLES.md` gains a partners section; `STRUCTURE.json` gains a
  `partners` array; each partner registers a `playbooks/partner_protocol_<name>.md`;
  interview Round 4 now asks for partners.
- **Invocation modes** — every rostered role declares `parallel-batch` / `singleton` /
  `external-bridge` (new column in `ROLES.md` + field in `STRUCTURE.json.agents`), so
  concurrency is a stated property of the role, not an improvisation. `WORKFLOW.md`
  fan-out section adds the rule: external-partner steps are async — never block the
  pipeline waiting on a contractor.
- **Layer ⑤: atomic + pipeline functions (Philosophy 3)** — the four-layer
  architecture becomes five, with an explicit creed: **"LLMs create and decide; code
  executes."** Atomic functions run one module exactly as coded; pipeline functions
  compose them, so even sequencing is code. Every decision the model repeats is a
  candidate for demotion into layer ⑤ (found by `/self-reflection-cli`).
- **Shared-capability reverse index** — `STRUCTURE.json.cli.consumers` maps each CLI
  subcommand to the skills/agents that consume it, so any change to shared
  "hardware" shows its blast radius.

### Changed
- Philosophy 3 renamed "Five-layer architecture (CEO → Sub-agent SP → Skill →
  MCP/CLI → Functions)"; README and the §F self-check updated to match.

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
