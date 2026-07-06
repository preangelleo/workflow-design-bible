# Changelog

All notable changes to the **Workflow Design Bible** are documented here.
This project follows [Semantic Versioning](https://semver.org/).

The skill version is mirrored in three places that must stay in sync:
`VERSION`, the `version:` field in `SKILL.md` frontmatter, and the top entry here.

## [2.4.0] — 2026-07-06

**Nine philosophies become ten**, and the QA/economics layer of the company model is
made explicit. The chairman's field feedback distilled: execution is functions, workers
self-check before reporting, internal staff before external APIs, and the CEO is reduced
to judgment + the closing motions. No breaking change to the document shape.

### Added
- **Philosophy 10 — Quality is a deterministic gate: no self-check, no report; no
  final QA, no ship.** Quality control is functions (CLI/MCP), never prose or a
  model's opinion: every sub-agent runs its step's `qa <step> <task_id>` and attaches
  the passing output to its report (an unattached report is invalid); the CEO runs the
  final `validate` sweep (counts + thresholds) and only a green result unlocks the
  `ship` CLI. Red checks are self-healing repair work; lowering a gate is an explicit,
  recorded org-level decision. Subjective residue (taste/brand fit) routes to a
  reviewer role/spot-check with playbook criteria — the exception, never the default.
  Lands in: `WORKFLOW.md.template` (new QA-chain section + `Final QA & ship` spine
  step), `ROLES.md.template` (the self-check contract), `CONSTITUTION.md.template`
  (Rule 4 rewritten from hand-verification to the QA chain), `STRUCTURE.json.template`
  (new `qa_chain` block), `/self-reflection` (new audit dimension 9).
- **Internal-first economics (Philosophy 2 + new Rule 9)** — never spend external
  LLM-API credits on work an internal sub-agent can do; internal dispatches ride the
  runtime already paid for. External models/partners are for genuine capability gaps,
  never convenience or capacity.
- **Per-step executing functions (Philosophy 3)** — the `WORKFLOW.md` spine gains an
  "Executes via (function/CLI)" column: every step names its atomic/pipeline function;
  a step with none is marked `[artisanal]` and tracked as **industrialization debt**
  (retired via `/self-reflection-cli`, registered in `qa_chain.artisanal_debt`).
- **CEO-reserved work made explicit (Philosophy 1)** — the CEO keeps: org-change
  decisions (workflow/doc/skill/CLI-function/role changes — decided by the CEO,
  executed by `dev-maintainer`), the final QA gate, the ship step, and the closing
  report to the chairman (shipped / cost / healed / org changes). Plus the context
  creed: handles, never payloads. `/finalize-session` step 9 becomes the chairman
  report; interview Round 4 now also asks each step's executing function + QA gate.

### Changed
- All-sub-agent scope spelled out (Philosophy 2): *all creation* (text, images, JSON
  artifacts) and *all maintenance* (functions, doc updates, MCP creation, scripts,
  cron jobs, code reviews) is employee work; **no skill-less roles** — every role
  names its paired skills, each skill declaring its MCP/CLI base.
- README and §F self-check updated to ten philosophies.

### Fixed
- Public-purity slip: a stray Chinese word in `self-reflection.SKILL.md.template`
  ("体检") replaced with English.

## [2.3.0] — 2026-07-04

Two **optional, protocol-only** reference playbooks for owners who run more than one
harness or more than one Bible-born project. No new philosophy, no structural change —
they are worked instances of the existing **external contract partner** abstraction
(Philosophy 2) and the constitution/Session-Bootstrap machinery. The Bible still ships
**no transport code**: each template defines only the contract; the project's
`dev-maintainer` writes the bridge/dispatch itself at build time.

### Added
- **`templates/documentation/playbooks/partner_protocol_codex.md.template`** — co-chair
  **second-harness** protocol: why a second harness is a *partner* not a *sub-agent*,
  the file-bridge shape (request → wake → response), the CEO-initiated delegation
  lifecycle, and the **chairman-bypass reconciliation report** (folds in the handoff /
  intake pattern). Gated: only seeded if the owner runs a co-chair harness.
- **`templates/documentation/playbooks/cross_project_visiting.md.template`** —
  **cross-project CEO visiting** protocol (a different axis: cross-project, same
  harness): outbound (read local law → idle-check → bounded task → leave a trace →
  return a compressed conclusion) and inbound (a visitor is bound by the host
  constitution with *less* authority than a resident, never more). Gated on a family of
  Bible-born projects.
- **SKILL.md** — Round 4 offers both as optional gated references; generation rule 6
  copies them only on opt-in. Frontmatter → 2.3.0.

## [2.2.0] — 2026-07-02

Field feedback absorbed from the deepest production instance of the Bible: three
mechanisms that real autonomous operation grew and validated, injected into the
existing philosophies (no new philosophy numbers, no structural change).

### Added
- **Self-healing invariant (Philosophy 1 + CONSTITUTION template)** — once a work
  unit is claimed, a repairable local fault is repair work, not a stop condition:
  smallest root cause → patch the owned layer → narrowest safe verification →
  resume the same task id. True stop conditions enumerated (missing credential /
  balance-payment failure / persistent third-party outage with no detour /
  irreversible external action / subjective judgment). New Rule 8 + extended
  standing authorization in `CONSTITUTION.md.template`.
- **Loop 0 — in-flight hotfix + upgrade-by-replacement (Philosophy 6 +
  CONSTITUTION template)** — patch the smallest live source future agents will
  read while the evidence is still in context; finalize summarizes fixes, it is
  never where one is first recorded. Living docs are current-state interfaces:
  rewrite old instructions, no "formerly X, now Y" sediment.
- **Semantic guards + drift ratchet (Philosophy 7 + STRUCTURE.json template +
  generation rule 7)** — `doctor` grows beyond existence checks: retired-phrase
  regex blacklist, entrypoint-pointer checks, meta-config validation,
  secret-hygiene scan; and every drift that escapes `doctor` must add the
  mechanical check that would have caught it. `doctor` only ever gains checks.
- §F self-check gains matching lines for all three.

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
