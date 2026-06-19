---
name: workflow-design-bible
description: Generate a complete "constitution + documentation system" for a new autonomous, agent-run project — a content channel, an ebook press, an SEO tool-site, a web product, a casual game, or anything that should run itself with minimal human babysitting. Use when starting a brand-new self-running project and you want a CEO-orchestrated architecture (main agent → sub-agents → skills → CLI/MCP) scaffolded from a short structured interview. Triggers - "start a new autonomous project", "scaffold a project constitution", "set up an agent-run project", "generate a CLAUDE.md / AGENTS.md for a new project", "design the workflow for X", "bootstrap a self-running pipeline".
version: 1.1.0
license: MIT
---

# Workflow Design Bible — a constitution generator for autonomous, agent-run projects

> **What this skill is.** A reusable *meta system prompt*. When the user wants to
> create a new autonomous project (content/video channel, ebook/publishing,
> SEO tool-site/wiki, web product, casual game, …), this skill runs a short
> **structured interview**, then **fills in a standard "constitution + docs"
> system**: a lean root `CLAUDE.md` (the project constitution) + a
> `documentation/configuration.json` + a `documentation/` folder of
> single-source-of-truth docs + empty `.claude/agents/`, `.claude/skills/`,
> `reflections/`, `reports/` skeletons.
>
> **What this skill does *not* do.** It runs **no business code**, writes **no
> application logic**, and deploys nothing. It only does **interview → generate
> the documentation skeleton + registries**. The real CLI (`factory.py` /
> `press.py` / whatever), the actual sub-agent system prompts, and each
> capability skill are grown *later* by the project's own CEO + maintainer agent.
>
> **Two ways to use it.** (1) *Read the doc* — paste this file into any capable
> LLM and follow it by hand. (2) *Install the skill* — drop this folder into your
> agent runtime's skills directory (e.g. a global `skills/` location) so it
> triggers automatically when you start a new project.
>
> **Templates live beside this file**, so the constitution stays lean:
> - `templates/CLAUDE.md.template` — the fill-in constitution skeleton.
> - `templates/configuration.json.template` — the brand single-source-of-truth.
>
> Throughout this document: **"CEO" = the main/orchestrating agent**;
> **"the user" = the human owner/chairman** who sets direction and signs off.
> Filenames like `CLAUDE.md` are conventions — substitute whatever your runtime
> reads as its top-level agent instructions (e.g. `AGENTS.md`).

---

## A. The eight non-negotiable design philosophies

These eight are the soul of the Bible. Every generated constitution **must**
embody all eight — they are not options, they are the foundation.

### Philosophy 1 · The main agent is a CEO, not a worker
The main agent's job is to **orchestrate, supervise, review, control the
process, and talk to the user** — not to do the manual labor itself. Spend its
context and reasoning on *judgment and coordination*. Every fixed, repeatable
step is delegated to a sub-agent by default.

### Philosophy 2 · Everything is a sub-agent; concurrency is the default latent power
**Every fixed work step is assigned to a role-clear sub-agent.** Because the work
is sub-agent-shaped, it is *natively parallelizable*:
- Independent steps → fan out at once (e.g. compile / cover / copywriting in parallel).
- Many homogeneous tasks of one kind → batch concurrency (e.g. 60 scenes, 50 in flight).

Express concurrency at the **fan-out points** of the pipeline (a dedicated
"parallelism" section), not as bookkeeping on every single agent. Caution against
*over-proliferation*: if one shared engine/codebase underlies many artifacts,
prefer **one shared maintainer agent** over a maintainer-per-artifact — split a
role out only when an artifact has genuinely distinct dependencies.

### Philosophy 3 · Four-layer architecture (CEO → Sub-agent SP → Skill → MCP/CLI)
Capabilities are tiered; each layer points **down**, and details **never** leak up:

```
① CEO (CLAUDE.md)            — assigns work, sets principles, touches no details
   ↓ dispatch a sub-agent with a self-contained task brief
② Sub-agent (its system prompt)
   — role definition + "which skills this role should mainly use" (pointers)
   ↓ invoke a skill
③ Skill (its SKILL.md)
   — how one capability is used; declares which MCP servers + which CLI commands it is built from
   ↓ execute
④ MCP servers + command-line tools (executed, never loaded into context)
```

**Key discipline:** a sub-agent can *see* a large pile of global + local skills,
but **seeing ≠ should-use**. Its system prompt must **explicitly narrow** ("your
work mainly uses skill X / Y") so it does not grab tools at random.

### Philosophy 4 · Single source of truth + pointers; the constitution never bloats
`CLAUDE.md` keeps only the **most critical, clearest** things: role definitions,
operating/management/orchestration principles, the pipeline spine (step by step),
and the file map. **All concrete common knowledge / single sources of truth sink
down into standalone docs under `documentation/`; the constitution holds only a
pointer + one line of context.** Likewise, push role detail down into each
sub-agent's system prompt — the CEO does not need to know *how* a role works, only
*that* it exists; it reads that SP when it needs the detail.
> Why: `CLAUDE.md` is resident in context on every interaction. Detail written
> inside it = carried as overhead, burning tokens, every single turn.

### Philosophy 5 · Two-tier capability layering: global (reuse) vs local (build)
Every constitution splits capabilities in two, and tells sub-agents the boundary:
- **Global (reuse, not built here):** skills / MCP / sub-agents shared across all
  your projects (a messaging capability, an image generator, a research helper,
  various operator agents). Call them directly, zero build cost.
- **Local (built/forked for this project):** the project-specific CLI, project
  skills, project sub-agents.
- ⚠️ **Scope trap:** a skill scoped to another project's directory will *not*
  auto-load here; fork a trimmed copy or call the global equivalent. State this
  in the generated constitution.

### Philosophy 6 · Reflection & self-iteration is always the last step — and there are two loops
Every pipeline's spine **ends** with *Reflection & Self-Iterate*: review this
cycle's flaws + efficiency → attribute root causes → dispatch the maintainer agent
to upgrade the process → consolidate. The goal is to steadily turn "still decided
on the fly" into "now frozen into a deterministic function." **No self-iteration
review = the loop did not close.** Two distinct loops exist; the constitution must
keep them separate:
- **Loop A — per-cycle reflection** (end of each pipeline run) → write to `reflections/` (permanent; never in a build dir that gets cleaned).
- **Loop B — cross-session async review**: a scheduled job writes a data/analytics
  report to `reports/`; the next session's **startup ritual** scans `reports/`,
  adopts/discards, then archives. Plus a `HANDOFF` note carries unfinished
  business between sessions.

**The durability failure to design around.** "Reflection is always the last step"
written as *prose* is an aspiration, not a guarantee: it lives only as "the agent
should remember to reflect at the end," so under deadline pressure it is the first
thing dropped, and within a few cycles the loop has quietly fallen into disuse —
the most common way a self-improving project stops improving. A loop that depends
on being *remembered* is not a loop. Make it survive disuse with two mechanics:
- **Trigger on the event, not on memory.** Bind reflection to a *change* rather
  than to a remembered habit. A meaningful state transition — a leaf change landing
  (CLI / skill / SP / template), a build dir filling, a cycle marked complete —
  should *fire* the reflection/consistency pass, not wait for the agent to recall
  it. Use a real event primitive (a pre-commit / pre-push hook, a file-watch on the
  build or `reflections/` dir, a CI step) so the loop runs because something
  changed. A coarse periodic fail-safe is fine as a backstop to bound worst-case
  staleness — but it is the backstop, never the primary cadence.
- **Converge the loop toward a deterministic anchor, not a vibe.** Open-ended "did
  we do well?" reflection erodes; reflection that ends by re-asserting a *machine-
  checkable* invariant compounds. That anchor is Philosophy 7's `doctor` (claims ==
  reality, **fail-closed**): the reflection step is "done" only when `doctor` exits
  zero. This is what fuses Loop A to Philosophy 7 — reflection stops being a soft
  retrospective and becomes "drive the project back to a green deterministic check."

### Philosophy 7 · Constitution-as-code: a deterministic self-check keeps claims == reality
A constitution drifts: it claims "9 sub-agents, 7 skills" while the filesystem says
otherwise. So every project ships a deterministic **`doctor` / consistency-lint**
command that checks *what the docs claim* against *what actually exists* (agents,
skills, CLI subcommands, asset counts, the lifeline DB) and **exits non-zero on
drift**. This is the machine backstop for the "propagation consistency" rule: any
leaf change (CLI/skill/SP/template) must, before it is done, re-confirm the upper
layers' contracts — and `doctor` enforces it mechanically.

Two properties make `doctor` actually load-bearing instead of decorative:
- **Fail-closed, not advisory.** A non-zero exit is a *gate*, not a warning the
  agent can talk past. Wire it where it blocks "done": a pre-commit / pre-push hook,
  a CI status check, the final assert of the reflection step. A `doctor` that only
  prints and exits zero is theater; drift slips through and the docs rot anyway.
- **Wired to a trigger, so it runs without being remembered.** The single most
  common way `doctor` fails is that it exists but nothing ever invokes it. Bind it
  to the change event (Philosophy 6's trigger mechanic) so a leaf change *cannot
  land green while the upper-layer contract is stale*. The discipline and the
  machine check are the same act: the leaf change fires the consistency pass, and
  the pass must end green.
> A reference recipe — a generic, language-agnostic `doctor` skeleton plus the
> change-event trigger that fires it — lives in
> [`templates/durable_self_iterate.md`](templates/durable_self_iterate.md). Drop a
> tuned copy into the generated project's `documentation/`.

### Philosophy 8 · Standard shape (root constitution + documentation/)
The project folder has a fixed shape from birth:
```
<project>/
├── CLAUDE.md              ← constitution: principles / spine / pointers / map (resident in context)
├── documentation/         ← every single source of truth / common knowledge / config (loaded on demand)
│   ├── configuration.json      brand structured config (single source of truth)
│   ├── INITIALIZATION.md       one-time first-deploy setup
│   ├── reflection_playbook.md  the step-by-step for the reflection step
│   ├── <playbook>.md           topic playbooks (quality gate / topic SOP / compliance / …)
│   └── …
├── .claude/agents/        ← sub-agent system prompts (role definitions)
├── .claude/skills/        ← each SKILL.md (capability definitions)
├── <core>.py / <core>/    ← deterministic CLI code (executed, never in context)
├── reflections/           ← per-cycle reflection reports (permanent)
├── reports/               ← async data/analytics reports (consumed at session start)
├── HANDOFF.md             ← cross-session handoff of unfinished work
├── CHANGELOG.md
└── <lifeline>.db          ← the lifeline memory store (gitignored)
```

---

## B. What one run of this skill delivers

| Deliverable | Path | Contents |
|---|---|---|
| **Project constitution** | `<project>/CLAUDE.md` | Filled in from `templates/CLAUDE.md.template` (lean, pointer-based). |
| **Brand config** | `<project>/documentation/configuration.json` | Filled in from `templates/configuration.json.template`. |
| **Initialization doc** | `<project>/documentation/INITIALIZATION.md` | Credential checklist + one-time deploy steps (skeleton). |
| **Topic single-source docs** | `<project>/documentation/<topic>.md` | The rules identified in the interview that don't belong in the constitution (one file each). |
| **Empty skeletons** | `.claude/agents/`, `.claude/skills/`, `reflections/`, `reports/`, `HANDOFF.md` | Create dirs + a `.gitkeep` or README placeholder each. |

> **Not generated:** business code, real sub-agent SP contents, real skill
> implementations — those grow after the project exists. This skill produces only
> the **registries + skeleton + placeholders**, registering up front *which roles
> and capabilities should exist* inside `CLAUDE.md`.

---

## C. Interaction protocol (the intake interview)

Complete the interview before generating. **Ask in rounds; offer multiple-choice
options wherever possible** to minimize the user's typing. After each round, echo
the answer back to confirm, then proceed. The goal: fill every `{{placeholder}}`
in the templates.

> **Efficiency rules for the interviewer:**
> - The CEO model, the five/six core rules, and the four-layer architecture are
>   **constants** across all projects — do *not* interview for them; they come
>   pre-filled from the template.
> - For global capabilities, **auto-survey the host environment first** (inspect
>   the available skills / sub-agent types / MCP servers your runtime exposes) and
>   **propose** a reuse list for the user to confirm or trim — do not ask the user
>   to recall them from memory.
> - Batch related questions (up to ~4 at a time). Aim to finish in 3–4 rounds, not 7.

### Round 0 · Project archetype (selects the pipeline template branch)
Ask which archetype the project is; each preloads a different spine draft:

| Archetype | Typical spine (preloaded draft, then tune with the user) |
|---|---|
| **Content — video/channel** | topic → script → render → publish → engage/comments → reflect |
| **Content — ebook/publishing** | thesis → write → compile → multi-channel distribute → marketing → optimize → reflect |
| **SEO traffic asset (tool-site/wiki)** | topic → build → ship → monetize → SEO/monitor → reflect |
| **Web product/site** | requirements → design → build → test → deploy → monitor → reflect |
| **Casual game** | concept → asset production (heavy batch concurrency) → build → test → publish → data-tune → reflect |
| **Other (custom)** | co-design the step-by-step from scratch |

### Round 1 · Mission, North Star, and red lines
- One-sentence mission (becomes the CEO's North Star).
- 3–5 **priority-ordered** non-negotiable constraints (earlier always outranks
  later). Guide the user to think through the chain (e.g. "account safety >
  content quality > quantity > speed > per-unit revenue").
- The **#1 iron law / compliance red line**, if any ("violate it and everything
  is wasted" — e.g. platform anti-spam, marketplace anti-AI-slop, ToS).
- **Don'ts (two tiers):** what is *Forbidden* (red-line actions that can void the
  whole project) vs *Discouraged* (avoid unless justified). These populate the
  template's **Don'ts** section, parallel to the Rules.

### Round 2 · Brand (→ configuration.json)
- Brand/codename? Attribution entity (real name? pen name? company)? Per-language?
- Slogan / tagline?
- Logo assets (any existing)?
- **Design language:** primary colors, fonts, tone keywords, visual style, taboos.
- Trust / E-E-A-T anchor (site / author identity)?
- Payment / account entity (if monetized)?

### Round 3 · Pipeline spine (step by step)
- Starting from the archetype draft, nail down **each step**: what it does → its
  output → the lead role.
- Mark **which steps can run in parallel** (the fan-out points).
- Confirm the last step = reflection & self-iteration.

### Round 4 · Roles (sub-agent split) + global/local capabilities
- One sub-agent per fixed step: name + one-line role + which step.
- There must always be a `dev-maintainer` (owns all code/SP/skill changes).
- Apply the anti-proliferation rule: prefer one shared maintainer unless an
  artifact has distinct dependencies.
- Confirm the auto-surveyed **global reuse list**; list the **local** skills to
  build/fork — and for each local skill, which MCP servers + which CLI commands it
  is built from (Philosophy 3, layer ③).
- For each sub-agent, name which skills it should mainly use (the pointer in its SP).

### Round 5 · Lifeline memory + single-source docs + phase/credentials
- What **long-term memory store** does the project need (asset portfolio DB /
  dedup vector store / progress ledger…)? What fields?
- Which rules are "single source of truth" and should each become a
  `documentation/<topic>.md`? (compliance, topic SOP, quality gate, pricing,
  ramp-up discipline…) Register each.
- Current phase? Ramp-up cadence / risk discipline?
- Which private credentials / external accounts must the user provide
  (→ INITIALIZATION.md)?
- The boundary for "only two reasons to stop and ask the user" (missing
  credential / subjective business judgment).

> When the interview is done, **echo back every filled key field** and confirm
> before writing any files.

---

## D. Generation rules

1. Copy `templates/CLAUDE.md.template`, replace every `{{…}}`, delete optional
   sections that don't apply. Keep it lean — **anything longer than a few lines
   becomes "one line + a pointer to `documentation/<x>.md`."**
2. Copy `templates/configuration.json.template`; put **all concrete brand values
   (pricing / colors / attribution) only here** — the constitution references
   them by pointer.
3. Generate `documentation/INITIALIZATION.md` (credentials + one-time deploy) and
   one `documentation/<topic>.md` per single-source rule identified in Round 5.
4. Create the empty skeletons (`.claude/agents/`, `.claude/skills/`,
   `reflections/`, `reports/`, `HANDOFF.md`) with placeholders.
5. Scaffold the durability loop, don't just recommend it (per Philosophies 6–7).
   Recommending a `doctor` "for later" is itself the durability failure — the
   anchor that's never built can't anchor anything. Instead:
   (a) drop a tuned copy of [`templates/durable_self_iterate.md`](templates/durable_self_iterate.md)
       into `documentation/`;
   (b) register a `{{core}} doctor` subcommand stub (it may start as a near-empty
       check that lists agents/skills/CLI subcommands and exits non-zero on a
       claim/reality mismatch — grown out later, but present and **fail-closed**
       from birth);
   (c) wire one change-event trigger that fires it (a pre-commit / pre-push hook or
       a file-watch on the build / `reflections/` dir), so the self-iterate loop
       runs on change, not on memory. A coarse periodic run is the fail-safe, not
       the primary cadence.

---

## E. After generation (the skill's closing actions)

1. **Self-check** against the checklist in §F.
2. Output a **manifest** to the user (what files were created + one line each), and
   restate the key decisions.
3. If your runtime hot-loads agents/skills, tell the user how to **reload** so the
   new `.claude/agents` / `.claude/skills` are picked up.
4. Point to the **next step:** the project CEO takes over → dispatches the
   maintainer agent to build out the registered local skills / sub-agents / CLI one
   by one.

---

## F. Quality self-check (must pass before delivery)

- [ ] `CLAUDE.md` contains **no rule longer than a few lines** — everything has sunk
      into `documentation/` or a sub-agent SP, leaving only pointers.
- [ ] All eight design philosophies are embodied (CEO model / all-sub-agent +
      concurrency / four-layer / single-source pointers / global-local tiers /
      reflection close with both loops / constitution-as-code self-check / standard shape).
- [ ] The pipeline's last step = reflection & self-iteration.
- [ ] The constitution has a **Rules** section *and* a parallel **Don'ts** section
      (Forbidden vs Discouraged clearly separated).
- [ ] Each sub-agent names the skills it should mainly use; concurrency is captured
      at the fan-out points.
- [ ] Each local skill states which MCP / CLI it is built from.
- [ ] Concrete brand values live only in `configuration.json`; the constitution is pointers.
- [ ] A `doctor` / consistency-lint command is **scaffolded** (not merely
      recommended) for the project CLI, and it is **fail-closed** (non-zero exit on
      drift).
- [ ] The self-iterate loop is **event-triggered** — at least one change-event hook
      (pre-commit / pre-push / file-watch) fires `doctor` / reflection on change;
      any periodic run is only a fail-safe, not the primary cadence.
- [ ] A tuned copy of `durable_self_iterate.md` is dropped into the project's
      `documentation/`.
- [ ] The file map matches the directory actually generated.
- [ ] Every `{{placeholder}}` has been replaced.
