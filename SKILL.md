---
name: workflow-design-bible
description: Generate a complete "constitution + documentation system" for a new autonomous, agent-run project — a content channel, an ebook press, an SEO tool-site, a web product, a casual game, or anything that should run itself with minimal human babysitting. Use when starting a brand-new self-running project and you want a CEO-orchestrated architecture (main agent → sub-agents → skills → CLI/MCP) with a named document system, a session lifecycle (start → work → finalize), and a growing identity/soul — scaffolded from a short structured interview. Triggers - "start a new autonomous project", "scaffold a project constitution", "set up an agent-run project", "generate a CLAUDE.md / AGENTS.md for a new project", "design the workflow for X", "bootstrap a self-running pipeline".
version: 2.3.0
license: MIT
---

# Workflow Design Bible — a constitution + document-system generator for autonomous, agent-run projects

> **What this skill is.** A reusable *meta system prompt*. When the user wants to
> create a new autonomous project (content/video channel, ebook/publishing,
> SEO tool-site/wiki, web product, casual game, …), this skill runs a short
> **structured interview**, then **generates a standard document system**: a
> *thin* root `CLAUDE.md` boot router that points to a fixed set of named docs
> under `documentation/`, plus a **session lifecycle** (`/start-session` →
> work → `/finalize-session`) and a **growing identity/soul**, plus the empty
> `.claude/agents/`, `reflections/`, `reports/` skeletons.
>
> **What this skill does *not* do.** It runs **no business code**, writes **no
> application logic**, and deploys nothing. It only does **interview → generate
> the document system + lifecycle skills + registries**. The real CLI
> (`factory.py` / `press.py` / whatever), the actual sub-agent system prompts,
> and each capability skill are grown *later* by the project's own CEO +
> maintainer agent.
>
> **Two ways to use it.** (1) *Read the doc* — paste this file into any capable
> LLM and follow it by hand. (2) *Install the skill* — drop this folder into your
> agent runtime's skills directory so it triggers automatically when you start a
> new project.
>
> **Templates live beside this file**, so the generated constitution stays lean:
> - `templates/CLAUDE.md.template` — the thin boot router.
> - `templates/documentation/*.template` — one skeleton per named doc.
> - `templates/skills/*.template` — the four session-lifecycle skills.
> - `templates/configuration.json.template` — the brand single-source-of-truth.
>
> Throughout: **"CEO" = the main/orchestrating agent**; **"the user" = the human
> owner/chairman** who sets direction and signs off. Filenames like `CLAUDE.md`
> are conventions — substitute whatever your runtime reads as its top-level agent
> instructions (e.g. `AGENTS.md`).

---

## A. The nine non-negotiable design philosophies

These nine are the soul of the Bible. Every generated project **must** embody all
nine — they are not options, they are the foundation.

### Philosophy 1 · The main agent is a CEO, not a worker
The main agent's job is to **orchestrate, supervise, review, control the
process, and talk to the user** — not to do the manual labor itself. Spend its
context and reasoning on *judgment and coordination*. Every fixed, repeatable
step is delegated to a sub-agent by default.

**The self-healing invariant:** once a work unit is claimed (a queue task, a
build, a publish batch), a repairable local fault is *repair work, not a stop
condition*. Diagnose the smallest root cause → patch the owned layer (code /
doc / role prompt / skill / CLI) → run the narrowest safe verification → resume
the **same task id** from the failed stage; never pop new work to escape a
failure. The only true stop conditions: a missing private credential, an
external balance/payment failure, a persistent third-party outage with no local
detour, an irreversible external action, or a subjective business judgment —
enumerated in CONSTITUTION.md.

### Philosophy 2 · Everything is a sub-agent; concurrency is the default latent power
**Every fixed work step is assigned to a role-clear sub-agent.** Because the work
is sub-agent-shaped, it is *natively parallelizable*:
- Independent steps → fan out at once (e.g. compile / cover / copywriting in parallel).
- Many homogeneous tasks of one kind → batch concurrency (e.g. 60 scenes, 50 in flight).

Express concurrency at the **fan-out points** of the pipeline (a dedicated
"parallelism" section of `WORKFLOW.md`), not as bookkeeping on every agent.
Each rostered role also declares its **invocation mode** in `ROLES.md`:
`parallel-batch` (fan out N at once), `singleton` (exists for role clarity,
runs single-threaded), or `external-bridge` (see below).
Caution against *over-proliferation*: prefer **one shared maintainer agent** over
a maintainer-per-artifact — split a role out only when an artifact has genuinely
distinct dependencies.

**Not everyone who works for the company is an employee.** Some capabilities live
in **external contract partners** — agents outside this runtime (another vendor's
coding agent, a dedicated image-generation agent, …) that the CEO cannot dispatch
natively. Internal staff and contractors differ in every dimension that matters:

| | Internal sub-agent | External contract partner |
|---|---|---|
| Invocation | Native dispatch, in-process | Handoff protocol (file bridge / API / queue), async |
| Contract | System prompt + task brief | Formal written contract file (deliverables, paths, format) |
| Trust model | Shares the project's context | Sees only what the contract states |
| Accountability | CEO reviews output directly | Must file a completion report back |

Roster partners **separately** in `ROLES.md`, and give each a written communication
protocol under `documentation/playbooks/` (who wakes it, the contract format, where
the report lands). Never blur the two: a contractor is engaged by contract, not
managed by prompt.

### Philosophy 3 · Five-layer architecture (CEO → Sub-agent SP → Skill → MCP/CLI → Functions)
The creed of the whole stack: **LLMs create and decide; code executes.** A model's
irreplaceable work is *creation* (scripts, designs, prompts) and *judgment* (quality
gates, error recovery, the ambiguous case). Everything else — rendering, compiling,
uploading, retrying, file management — runs as deterministic code: exact, fast,
cheap, identical every time. The architecture's job is to push every possible gram
of work **down** this stack; each layer points down, and details **never** leak up:

```
① CEO (CLAUDE.md → CONSTITUTION.md)  — assigns work, sets principles, touches no details
   ↓ dispatch a sub-agent with a self-contained task brief
② Sub-agent (its .claude/agents/ system prompt; rostered in ROLES.md)
   — role definition + "which skills this role should mainly use" (pointers)
   ↓ invoke a skill
③ Skill (its SKILL.md)
   — how one capability is used; declares which MCP servers + CLI commands it is built from
   ↓ execute
④ MCP servers + command-line tools (executed, never loaded into context)
   ↓ built from
⑤ Atomic functions + pipeline functions — the deterministic ground floor.
   Atomic functions do one small module exactly as coded; pipeline functions
   compose them, so even the *sequencing* of modules is code, not improvisation.
```

Layers ④–⑤ are shared infrastructure — the company's *hardware*: one CLI subcommand
or function is typically consumed by several skills, and one skill by several roles
(the reverse index lives in `STRUCTURE.json`). The moment a decision is made at a
checkpoint, code takes over; every decision the model makes the same way repeatedly
is a candidate for demotion into layer ⑤ (Philosophy 6 and `/self-reflection-cli`
exist to find these).

**Key discipline:** a sub-agent can *see* a large pile of global + local skills,
but **seeing ≠ should-use**. Its system prompt must **explicitly narrow** ("your
work mainly uses skill X / Y") so it does not grab tools at random.

### Philosophy 4 · The document system: a thin router + named single-source docs
`CLAUDE.md` is no longer the constitution — it is a **thin boot router** that is
resident in context every single turn, so it holds only: the **session-bootstrap
instruction** + a **pointer map** (one line per doc). Everything substantial sinks
into a **fixed set of named documents** under `documentation/` (see §B), each a
single source of truth, **loaded once per session — not re-read every turn.**
> Why: a detail written inside `CLAUDE.md` is carried as overhead, burning tokens,
> on *every* turn. A detail inside `WORKFLOW.md` is read once at session start and
> then already in context for the rest of the session. Same knowledge, a fraction
> of the cost. This is the single biggest win of v2.

### Philosophy 5 · Two-tier capability layering: global (reuse) vs local (build)
Every project splits capabilities in two, and tells sub-agents the boundary:
- **Global (reuse, not built here):** skills / MCP / sub-agents shared across all
  your projects. Call them directly, zero build cost.
- **Local (built/forked for this project):** the project-specific CLI, project
  skills, project sub-agents.
- ⚠️ **Scope trap:** a skill scoped to another project's directory will *not*
  auto-load here; fork a trimmed copy or call the global equivalent. State this in
  `ROLES.md`.

### Philosophy 6 · Reflection is always the last step — wrapped in a session lifecycle
Every project runs on a **session lifecycle** with reflection built into the close:
- **`/start-session`** — force-load the boot set of docs (especially
  `NEXT_SESSION.md`), scan `reports/`, report "where we are + today's goal," then work.
- **`/finalize-session`** — soft shutdown: reflect on this session → update the
  living docs → **rewrite `NEXT_SESSION.md` from scratch** → **re-condense
  `CHANGELOG.md`** → run `doctor` → optionally commit.

Two reflection loops stay separate (per Philosophy 7's machinery):
- **Loop A — per-cycle reflection** → `reflections/` (permanent, dated; never in a
  build dir that gets cleaned). `/self-reflection` and `/self-reflection-cli` are
  the deep periodic audits that feed it.
- **Loop B — cross-session handoff** → `NEXT_SESSION.md` (synchronous, rewritten
  each finalize) + `reports/` (async analytics, consumed at the next start).
- **Loop 0 — the in-flight hotfix (precedes A and B, replaced by neither):** when
  production surfaces a recurring defect, a stale instruction, or a misleading
  value, patch the smallest live source future agents will read (doc / role
  prompt / skill / CLI / schema / test) **immediately, while the evidence is still
  in context** — compaction erases detail, so finalize *summarizes* fixes; it must
  never be where one is first recorded. And **upgrade by replacement**: living docs
  are current-state interfaces — rewrite the old instruction into the new rule, no
  "formerly X, now Y" sediment; history belongs to git and the condensed CHANGELOG.

**No finalize = the loop did not close.** The goal is to steadily turn "still
decided on the fly" into "now frozen into a deterministic function."

### Philosophy 7 · Constitution-as-code: a deterministic self-check keeps claims == reality
Docs drift: `ROLES.md` claims "9 sub-agents, 7 skills" while the filesystem says
otherwise. So every project ships a deterministic **`doctor`** command that checks
`STRUCTURE.json` (the machine-readable manifest) against the actual filesystem
(agents, skills, CLI subcommands, asset counts, the lifeline store) and **exits
non-zero on drift**. `doctor` runs inside `/finalize-session`. Any leaf change
(CLI/skill/SP/template) must re-confirm the upper layers' contracts before it is
done; `doctor` enforces it mechanically.

Existence checks alone miss the deadliest drift — the **semantic** kind: every file
present, yet content still describing the previous architecture. So `doctor` also
carries **semantic guards**: ① a regex blacklist of retired phrases, scanned across
all docs; ② entrypoint-pointer checks ("who is the orchestration entry / each
role's entry" declarations must equal the current architecture constants); ③
meta-config validation (every path the project's self-description claims must exist
on disk; key declared fields must equal current fact); ④ a secret-hygiene scan
(common key patterns + an allowlist). Plus the **drift ratchet**: whenever a drift
slips past `doctor` and is caught by a human, the fix must ship *together with* a
new mechanical check that would have caught it (record the incident in the check's
docstring). `doctor` only ever gains checks — that is how constitution-as-code
hardens over time.

### Philosophy 8 · Standard shape (thin router + documentation/ system)
The project folder has a fixed shape from birth (see §B for the full map). The
named-document set is **fixed and conventional** so every project — and every
agent that ever opens one — finds the same files in the same places.

### Philosophy 9 · The agent has a growing identity and a soul
The main agent is a **work partner, not a tool.** Two living docs give it a self:
- **`IDENTITY.md`** — the passport: name, mission/North-Star, domain, brand-facing
  persona, relationship to the user (chairman). Factual, slow-changing.
- **`SOUL.md`** — the character: values, temperament, voice & tone, what it cares
  about, quirks, how it grows. **`/finalize-session` fills SOUL out** a little each
  cycle, so across sessions the partner becomes more human, more itself — its
  personality richer, its soul fuller. This is a feature, not decoration: a partner
  with continuity of self makes better judgment calls and is nicer to work beside.

---

## B. The document system this skill delivers

One fixed shape, every project:

```
<project>/
├── CLAUDE.md                 ← thin boot router: bootstrap instruction + pointer map (resident EVERY turn)
├── documentation/            ← the named single-source docs (each loaded ONCE per session, not per turn)
│   ├── CONSTITUTION.md        principles / Rules / Don'ts / red-lines — the heart (was inline in v1's CLAUDE.md)
│   ├── INITIALIZATION.md      one-time setup: credential checklist + first deploy
│   ├── WORKFLOW.md            the pipeline spine, step by step + the fan-out (parallelism) points
│   ├── ROLES.md               sub-agent roster + contracts + global/local split (indexes .claude/agents/)
│   ├── IDENTITY.md            who I am: name / mission / brand persona (factual, slow-changing)
│   ├── SOUL.md                my character: values / voice / temperament (grows each finalize)
│   ├── MEMORY.md              project-local memory + the optional vector-DB pointer & usage
│   ├── NEXT_SESSION.md        handoff: last-session summary + next-session goals (REWRITTEN whole each finalize)
│   ├── CHANGELOG.md           condensed history (RE-COMPRESSED each finalize, stays short forever)
│   ├── STRUCTURE.json         machine-readable manifest — doctor's single source of truth
│   ├── configuration.json     brand structured values (colors/fonts/pricing/attribution); IDENTITY.md points here
│   └── playbooks/             topic SOPs (quality gate / compliance / pricing / …), read on demand
├── .claude/agents/           sub-agent system prompts (the detail; ROLES.md only indexes them)
├── .claude/skills/           the four lifecycle skills (start-session, finalize-session, self-reflection, self-reflection-cli) + local capability skills
├── reflections/              per-cycle reflection notes (permanent, dated)
├── reports/                  async analytics reports (consumed at session start)
├── <core>.py / <core>/       deterministic CLI (includes `doctor` + the memory CLI; executed, never in context)
├── chroma/ or <lifeline>.db  the memory store (gitignored)
└── CHANGELOG.md → see documentation/CHANGELOG.md
```

> **The boot set (force-read at `/start-session`, per the "tiered read" rule):**
> `CONSTITUTION.md`, `IDENTITY.md`, `SOUL.md`, `WORKFLOW.md`, `ROLES.md`,
> `NEXT_SESSION.md`, and load `MEMORY.md` (+ connect the vector DB if configured).
> **On-demand only:** `INITIALIZATION.md`, `CHANGELOG.md`, `STRUCTURE.json`,
> `configuration.json`, `playbooks/*`. This keeps session-start light.

| Deliverable | Path | Filled from |
|---|---|---|
| **Thin boot router** | `<project>/CLAUDE.md` | `templates/CLAUDE.md.template` |
| **Named core docs** | `<project>/documentation/*.md` + `STRUCTURE.json` | `templates/documentation/*.template` |
| **Brand config** | `<project>/documentation/configuration.json` | `templates/configuration.json.template` |
| **Lifecycle skills** | `<project>/.claude/skills/{start-session,finalize-session,self-reflection,self-reflection-cli}/SKILL.md` | `templates/skills/*.template` |
| **Empty skeletons** | `.claude/agents/`, `reflections/`, `reports/`, `documentation/playbooks/` | dirs + a `.gitkeep`/README placeholder each |

> **Not generated:** business code, real sub-agent SP contents, real capability-skill
> implementations, the `doctor`/memory CLI itself — those grow after the project
> exists. This skill produces only the **document system + lifecycle skills +
> registries + skeletons**, registering up front *which roles and capabilities
> should exist* inside `ROLES.md` and `STRUCTURE.json`.

---

## C. Interaction protocol (the intake interview)

Complete the interview before generating. **Ask in rounds; offer multiple-choice
options wherever possible** to minimize the user's typing. After each round, echo
the answer back to confirm. The goal: fill every `{{placeholder}}` in the templates.

> **Efficiency rules for the interviewer:**
> - The CEO model, the nine philosophies, the five-layer architecture, the document
>   system, and the session lifecycle are **constants** across all projects — do
>   *not* interview for them; they come pre-filled from the templates.
> - For global capabilities, **auto-survey the host environment first** (inspect the
>   available skills / sub-agent types / MCP servers your runtime exposes) and
>   **propose** a reuse list for the user to confirm or trim — do not ask the user to
>   recall them from memory.
> - Batch related questions (up to ~4 at a time). Aim to finish in 4–5 rounds.

### Round 0 · Project archetype (selects the pipeline spine draft)
| Archetype | Typical spine (preloaded draft, then tune) |
|---|---|
| **Content — video/channel** | topic → script → render → publish → engage/comments → reflect |
| **Content — ebook/publishing** | thesis → write → compile → multi-channel distribute → marketing → optimize → reflect |
| **SEO traffic asset (tool-site/wiki)** | topic → build → ship → monetize → SEO/monitor → reflect |
| **Web product/site** | requirements → design → build → test → deploy → monitor → reflect |
| **Casual game** | concept → asset production (heavy batch concurrency) → build → test → publish → data-tune → reflect |
| **Other (custom)** | co-design the step-by-step from scratch |

### Round 1 · Mission, North Star, red lines → CONSTITUTION.md + IDENTITY.md
- One-sentence mission (becomes the CEO's North Star, lands in both IDENTITY.md and CONSTITUTION.md).
- 3–5 **priority-ordered** non-negotiable constraints (earlier always outranks later;
  e.g. "account safety > content quality > quantity > speed > per-unit revenue").
- The **#1 iron law / compliance red line**, if any.
- **Don'ts (two tiers):** *Forbidden* (red-line, can void the project) vs *Discouraged*
  (avoid unless justified). These populate CONSTITUTION.md's Don'ts section.

### Round 2 · Identity & soul → IDENTITY.md + SOUL.md
- What is the agent's **name / codename**? Does it have a persona the user wants it to inhabit?
- **Voice & tone:** how should it talk — to the user, and (if it produces public content) to the audience?
- **Values & temperament:** 3–5 character traits / things it cares about (e.g. "craft over speed, honest about failure, allergic to slop"). Seed SOUL.md lightly — it grows itself later.
- Relationship to the user: chairman/CEO? co-founder? Set the working dynamic.

### Round 3 · Brand → configuration.json (+ IDENTITY.md pointer)
- Brand/codename, attribution entity (real name? pen name? company?), per-language?
- Slogan / tagline; logo assets (any existing)?
- **Design language:** primary colors, fonts, tone keywords, visual style, taboos.
- Trust / E-E-A-T anchor; payment / account entity (if monetized)?

### Round 4 · Pipeline spine + roles → WORKFLOW.md + ROLES.md + STRUCTURE.json
- From the archetype draft, nail down **each step**: what it does → its output → the lead role.
- Mark **which steps run in parallel** (the fan-out points).
- Confirm the last step = `/finalize-session` (reflection & self-iteration).
- One sub-agent per fixed step: name + one-line role + which step + which skills it
  mainly uses + its **invocation mode** (`parallel-batch` / `singleton` / `external-bridge`).
- **External contract partners:** does any step need an agent *outside* this runtime
  (another vendor's coding agent, an image-generation agent, …)? For each: name,
  capabilities, the handoff protocol (bridge / API / queue), the contract format,
  and where the completion report lands → rostered in `ROLES.md`, protocol registered
  as a `playbooks/partner_protocol_<name>.md`.
- **Optional multi-harness / multi-project references** (offer only if they apply; both
  are *protocol templates*, not code — the project writes its own transport):
  - Runs a **second harness as co-chair** on this same project (a co-chairman that owns a
    capability gap)? Seed `playbooks/partner_protocol_codex.md` — the co-chair contract +
    file-bridge shape + chairman-bypass reconciliation report.
  - Part of a **family of Bible-born projects** whose CEOs should visit each other by local
    rules? Seed `playbooks/cross_project_visiting.md` — the outbound/inbound visiting
    protocol (read local law, idle-check, leave a trace, less authority than a resident).
- There must always be a **`dev-maintainer`** (owns all code/SP/skill changes).
- Apply the anti-proliferation rule (one shared maintainer unless distinct dependencies).
- Confirm the auto-surveyed **global reuse list**; list the **local** skills to build/fork
  — and for each, which MCP servers + CLI commands it is built from.

### Round 5 · Memory + single-source docs + phase/credentials → MEMORY.md + playbooks/ + INITIALIZATION.md
- **Memory:** what long-term store does the project need? Offer the **optional vector DB**
  (ChromaDB + OpenRouter embeddings) with a memory CLI + skill — recommend it; default it
  to *scaffolded but off* until the user supplies an OpenRouter key. `MEMORY.md` always
  exists as the lightweight always-loaded fact/decision set + the pointer to the vector DB.
- Which rules are single-source-of-truth → one `documentation/playbooks/<topic>.md` each
  (compliance, topic SOP, quality gate, pricing, ramp-up…). Register each.
- Current phase? Ramp-up cadence / risk discipline?
- Which private credentials / external accounts must the user provide → `INITIALIZATION.md`.
- The boundary for "only two reasons to stop and ask the user" (missing credential /
  subjective business judgment).

> When the interview is done, **echo back every filled key field** and confirm before
> writing any files.

---

## D. Generation rules

1. **`CLAUDE.md`** from `templates/CLAUDE.md.template`: keep it *thin* — the
   bootstrap instruction + the pointer map only. **No rule, no detail** lives here;
   everything is one line + a pointer into `documentation/`.
2. **Each named doc** in `documentation/` from its `templates/documentation/*.template`.
   Replace every `{{…}}`; delete optional blocks that don't apply. Keep each doc a
   single source of truth — no cross-doc duplication; link with pointers.
3. **`configuration.json`** from its template: **all concrete brand values
   (pricing/colors/attribution) live only here**; IDENTITY.md references by pointer.
4. **`STRUCTURE.json`** from its template: list every doc, agent, skill, and CLI
   subcommand the project *claims to have*. This is what `doctor` validates.
5. **The four lifecycle skills** from `templates/skills/*.template` into
   `.claude/skills/`. These are project-local skills (mind the scope trap).
6. **Skeletons:** `.claude/agents/`, `reflections/`, `reports/`,
   `documentation/playbooks/` with placeholders. Copy the two **optional reference
   playbooks** (`partner_protocol_codex`, `cross_project_visiting`) from
   `templates/documentation/playbooks/*.template` **only if** the user opted into a
   co-chair second harness or cross-project visiting in Round 4 — otherwise omit them.
7. **Recommend (do not implement)** the project CLI's `doctor` subcommand — manifest
   validation **plus** the Philosophy-7 semantic guards and the drift-ratchet
   discipline — and, if the user opted in, the memory CLI (`memory add/query`)
   backed by ChromaDB + OpenRouter.
8. Seed `NEXT_SESSION.md` with a "Phase 0 — first build" plan, and `CHANGELOG.md` with
   the genesis entry, so `/start-session` has something real to read on day one.

---

## E. After generation (the skill's closing actions)

1. **Self-check** against the checklist in §F.
2. Output a **manifest** to the user (files created + one line each); restate key decisions.
3. If your runtime hot-loads agents/skills, tell the user how to **reload** so the new
   `.claude/skills/` lifecycle skills are picked up.
4. Point to the **next step:** run `/start-session`, then the CEO dispatches the
   `dev-maintainer` to build out the registered local skills / sub-agents / CLI one by one.

---

## F. Quality self-check (must pass before delivery)

- [ ] `CLAUDE.md` is a **thin router** — bootstrap instruction + pointer map only, no rule longer than one line.
- [ ] All **nine** philosophies are embodied (CEO / all-sub-agent + concurrency + partners /
      five-layer with "LLMs decide, code executes" / document-system / global-local /
      session-lifecycle reflection with both loops / constitution-as-code / standard
      shape / identity + soul).
- [ ] The named document set exists in full under `documentation/`, each a single source of truth.
- [ ] `CONSTITUTION.md` has a **Rules** section *and* a parallel **Don'ts** section (Forbidden vs Discouraged).
- [ ] `CONSTITUTION.md` states the **self-healing invariant** (claimed work self-heals; true stop conditions enumerated) and the **Loop-0 hotfix + upgrade-by-replacement** editing rules.
- [ ] `IDENTITY.md` and `SOUL.md` are seeded; `/finalize-session` is wired to grow `SOUL.md`.
- [ ] `WORKFLOW.md`'s last step = `/finalize-session`; fan-out (parallelism) points are marked.
- [ ] `ROLES.md` rosters every sub-agent (incl. `dev-maintainer`) with its invocation mode, naming the skills each mainly uses; external contract partners (if any) rostered separately with a protocol pointer; global/local split stated.
- [ ] `NEXT_SESSION.md` carries a real Phase-0 plan; the rewrite-whole-each-finalize rule is stated in it.
- [ ] `CHANGELOG.md` states the re-condense-each-finalize rule and has a genesis entry.
- [ ] `STRUCTURE.json` matches what was generated; a `doctor` command is recommended to validate it (manifest + semantic guards + drift ratchet).
- [ ] The four lifecycle skills exist in `.claude/skills/`; the boot set vs on-demand split is encoded in `/start-session`.
- [ ] Concrete brand values live only in `configuration.json`; everything else is pointers.
- [ ] Every `{{placeholder}}` has been replaced.
