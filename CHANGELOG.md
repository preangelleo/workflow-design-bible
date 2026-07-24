# Changelog

All notable changes to the **Workflow Design Bible** are documented here.
This project follows [Semantic Versioning](https://semver.org/).

The skill version is mirrored in three places that must stay in sync:
`VERSION`, the `version:` field in `SKILL.md` frontmatter, and the top entry here.

## [2.6.0] — 2026-07-25

**Mode B: restructure an existing project — survey, don't interview.** Recovered from
field drift: this section was written directly into an installed copy during a real
v1→v2 project restructure and never made it back to the source until now.

### Added
- **§C Mode B** — when the target project already exists, skip the interview and
  **survey** instead: derive every `{{placeholder}}` from the project's own docs, git
  history, agents, and skills; ask only about genuine gaps or judgment calls. Ships
  four battle-tested rules: the **name-collision rule** (a pre-existing domain
  constitution is renamed via `git mv`, the Bible slot goes to the operating
  constitution, supremacy order stated); **migrate, don't duplicate** (existing
  single-source docs `git mv` into `playbooks/`, all references swept, old paths
  registered as `retired_phrases` so `doctor` blocks regressions); **retire the old
  handoff file** into `NEXT_SESSION.md`; feature-branch + green-`doctor` shipping.

## [2.5.0] — 2026-07-25

**Craft distilled from the wild.** This release absorbs the essence of Matt Pocock's
[`mattpocock/skills`](https://github.com/mattpocock/skills) (MIT) — a battle-tested
library of agent-engineering disciplines — into the Bible's existing philosophies and
templates. No new philosophy, no structural change to the document shape: every
adoption lands as a sharpening of what was already there.

### Added
- **Two standard reference playbooks**, now copied into *every* generated project
  (the previous two playbooks remain optional/gated):
  - `playbooks/skill_authoring.md` — how skills/docs/role prompts are written:
    predictability as the root virtue, model-invoked vs user-invoked (context load
    vs cognitive load), one-trigger-per-branch descriptions, progressive disclosure,
    leading words, the no-op test, positive phrasing over negation, checkable
    completion criteria, and a failure-mode diagnosis table.
  - `playbooks/campaign_map.md` — planning efforts bigger than one session:
    destination-first decision tickets, the fog-of-war test ("can you state the
    question precisely now"), HITL vs AFK ticket types, claim-before-work,
    one-decision-per-session, out-of-scope that never graduates, and handoff as
    vertical slices sized to one fresh session.
- **CONSTITUTION template** — four sharpenings: ① *facts vs decisions* in the
  standing authorization (look up facts, ask only decisions — every question carries
  a recommended answer); ② new Rule 9 **loop before hypothesis** (build a tight,
  red-capable feedback loop before theorizing; the same loop green = verified);
  ③ a **Ruled-out-of-scope ledger** mirroring the locked decisions (rejections are
  as binding as decisions); ④ the locked-decisions gate (a row must be hard to
  reverse, surprising without context, and a real trade-off) + a context-hygiene
  line (don't push on degraded context — finalize and continue fresh).
- **MEMORY template** — a **domain glossary** section (ubiquitous language): tight
  is-not-does definitions with `_Avoid:` alias lists, updated the moment a term
  settles; one settled word replaces twenty every future sentence.
- **ROLES template** — **task-brief discipline** ("the brief is the contract"):
  behavioral not procedural; durability over precision (no file paths / line
  numbers in briefs read sessions later); checkable acceptance criteria; explicit
  out-of-scope against gold-plating.
- **WORKFLOW template** — multi-axis review isolation: one reviewer per axis in
  parallel, verdicts kept side by side (merging lets one axis mask the other).
- **SKILL.md** — Philosophy 1 gains loop-first diagnosis; Philosophy 2 gains the
  **design-it-twice** fan-out (2–3 agents design independently, judge side by
  side); Philosophy 4 gains **the writing discipline** block (pointer to the
  `skill_authoring` playbook); the interview gains the facts-vs-decisions and
  recommended-answer-per-question rules, **frontier rounds** (the round order is a
  default, not a script — each round asks every question whose prerequisites are
  settled, skipping what earlier answers already settled) and a **downshift to
  grilling** (a vague or contradictory answer switches that one thread to
  one-question-at-a-time until sharp); Round 5 seeds the glossary.
- **finalize-session skill** — glossary upkeep + "decision-grade insights, not a
  journal" bar for memory growth.

### Credits
- Ideas distilled (and re-voiced for the Bible's architecture) from
  [`mattpocock/skills`](https://github.com/mattpocock/skills), MIT © Matt Pocock.

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
