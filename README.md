# Workflow Design Bible

**A constitution + document-system generator for autonomous, agent-run projects.**

`workflow-design-bible` is a reusable **meta system prompt** (and an installable
agent skill). Point it at a brand-new project idea — a content channel, an ebook
press, an SEO tool-site, a web product, a casual game, anything meant to *run
itself* — and it interviews you, then scaffolds a complete, opinionated workflow:

- a **thin `CLAUDE.md` boot router** that stays resident every turn but holds almost
  nothing;
- a **document system** under `documentation/` — a fixed set of named single-source
  docs (`CONSTITUTION.md`, `WORKFLOW.md`, `ROLES.md`, `IDENTITY.md`, `SOUL.md`,
  `MEMORY.md`, `NEXT_SESSION.md`, …) loaded **once per session, not every turn**;
- a **session lifecycle** — `/start-session` → work → `/finalize-session` — so each
  session boots from where the last one left off and ends by improving itself;
- a **growing identity and soul**, so the agent becomes a work *partner* that gets
  more itself over time, not a stateless tool.

It encodes one architecture that has proven itself on real autonomous projects:

> **The main agent is a CEO, not a worker.** It orchestrates; sub-agents do the
> work; skills document capabilities; a deterministic CLI executes them. Every
> session ends by reflecting on itself and freezing "decided on the fly" into
> "frozen into a function."

— distilled into **nine design philosophies** every generated project inherits.

---

## Why this exists

LLM agents are great at *doing a task* and bad at *running a project*. Without a
durable structure they re-decide the same things every session, bloat their own
context, lose track of what's already built, forget who they are, and never improve
their own process.

This Bible bakes in the structure. The biggest win in **v2** is the
*reload economics*: only the thin router rides in context every turn; the heavy
knowledge sits in named docs read **once per session**. Same knowledge, a fraction
of the token cost — and a context window that fills up far more slowly. On top of
that: a CEO/sub-agent split, five clean capability layers, a session lifecycle that
carries state forward, a built-in self-consistency check, and an identity/soul that
compounds. You answer a short interview; you get a project that knows how to run,
remember, and improve itself.

## The nine design philosophies

1. **The main agent is a CEO, not a worker** — orchestrate, don't do manual labor.
2. **Everything is a sub-agent; concurrency is the default** — fan out at the seams; each role declares its invocation mode, and external contract partners are rostered separately from internal staff (engaged by contract, not managed by prompt).
3. **Five-layer architecture** — CEO → sub-agent prompt → skill → MCP/CLI → deterministic functions; **LLMs create and decide, code executes**; pointers down, details never up.
4. **The document system** — a thin `CLAUDE.md` router + named single-source docs loaded once per session, not every turn.
5. **Global vs local capabilities** — reuse what's shared, build only what's unique.
6. **Reflection is the last step, wrapped in a session lifecycle** — `/start-session` and `/finalize-session`, two loops.
7. **Constitution-as-code** — a `doctor` command keeps `STRUCTURE.json`'s claims equal to filesystem reality.
8. **A standard project shape** — thin router + `documentation/` system from birth.
9. **The agent has a growing identity and soul** — `IDENTITY.md` + `SOUL.md`; it becomes more itself each cycle.

Full detail lives in [`SKILL.md`](./SKILL.md).

## How to use it

**Option A — read the doc.** Open [`SKILL.md`](./SKILL.md) (and the
[`templates/`](./templates)) and paste it into any capable LLM. Follow the
interview protocol; fill in the templates by hand. No runtime required.

**Option B — install the skill.** Copy this folder into your agent runtime's
skills directory so it triggers automatically when you start a new project. For
[Claude Code](https://claude.com/claude-code), for example:

```bash
# global install (available in every working directory)
git clone https://github.com/preangelleo/workflow-design-bible.git \
  ~/.claude/skills/workflow-design-bible
# then reload skills in your session
```

Then just say *"start a new autonomous project"* (or describe the project) and the
skill runs the interview and generates the constitution + document system. Once
generated, run `/start-session` in the new project to boot it.

> Filenames like `CLAUDE.md` are conventions — substitute whatever your runtime
> reads as top-level agent instructions (e.g. `AGENTS.md`). The ideas are
> runtime-agnostic.

## What's in this repo

| Path | What it is |
|---|---|
| [`SKILL.md`](./SKILL.md) | The meta system prompt: nine philosophies + interview protocol + generation rules. The main artifact. |
| [`templates/CLAUDE.md.template`](./templates/CLAUDE.md.template) | The thin boot-router skeleton. |
| [`templates/documentation/`](./templates/documentation) | One skeleton per named doc (CONSTITUTION, WORKFLOW, ROLES, IDENTITY, SOUL, MEMORY, NEXT_SESSION, CHANGELOG, INITIALIZATION, STRUCTURE.json). |
| [`templates/skills/`](./templates/skills) | The four session-lifecycle skills (start-session, finalize-session, self-reflection, self-reflection-cli). |
| [`templates/configuration.json.template`](./templates/configuration.json.template) | The brand single-source-of-truth. |
| [`CHANGELOG.md`](./CHANGELOG.md) · [`VERSION`](./VERSION) | Version history + current version. |

## What it does *not* do

It runs no business code, writes no application logic, and deploys nothing. It only
does **interview → generate the document system + lifecycle skills + registries**.
The actual CLI (incl. `doctor` and the optional memory store), the sub-agent system
prompts, and capability skills are grown later by the project's own CEO + maintainer
agent.

## Versioning & upgrades

Semantic versioning. The version is mirrored in `VERSION`, the `version:` field of
`SKILL.md` frontmatter, and `CHANGELOG.md`. To upgrade an installed copy, pull the
repo (or re-clone) and reload your skills. Watch releases for new versions.

> **v2.0.0 is a structural evolution from v1**: v1 put the whole constitution inside
> `CLAUDE.md`; v2 makes `CLAUDE.md` a thin router and moves the content into a named
> document system loaded once per session, adds the start/finalize session lifecycle,
> and gives the agent a growing identity/soul. New projects should generate on v2.
>
> Later minor releases refine the org and execution model — external contract
> partners, per-role invocation modes, the five-layer "LLMs create and decide;
> code executes" stack, the production self-healing invariant, in-flight hotfix
> discipline, and `doctor`'s semantic guards + drift ratchet. Full history →
> [`CHANGELOG.md`](./CHANGELOG.md). (This paragraph is rewritten, not appended to,
> each release.)

## Contributing

Issues and PRs welcome — especially new project archetypes, sharper interview
questions, and template improvements. Keep the core principle intact: the router
stays thin; detail sinks into the document system as pointers.

## License

[MIT](./LICENSE) © 2026 preangelleo
