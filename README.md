# Workflow Design Bible

**A constitution generator for autonomous, agent-run projects.**

`workflow-design-bible` is a reusable **meta system prompt** (and an installable
agent skill). Point it at a brand-new project idea — a content channel, an ebook
press, an SEO tool-site, a web product, a casual game, anything meant to *run
itself* — and it interviews you, then scaffolds a complete **"constitution +
documentation system"**: a lean root `CLAUDE.md`, a brand `configuration.json`,
and a `documentation/` folder of single-source-of-truth docs, plus the empty
`.claude/agents/` and `.claude/skills/` registries to grow into.

It encodes one opinionated architecture that has proven itself on real autonomous
projects:

> **The main agent is a CEO, not a worker.** It orchestrates; sub-agents do the
> work; skills document capabilities; a deterministic CLI executes them. Every
> pipeline ends by reflecting on itself and freezing "decided on the fly" into
> "frozen into a function."

— distilled into **eight design philosophies** every generated project inherits.

---

## Why this exists

LLM agents are great at *doing a task* and bad at *running a project*. Without a
durable structure they re-decide the same things every session, bloat their own
context, lose track of what's already built, and never improve their own process.

This Bible bakes in the structure: a CEO/sub-agent split, four clean capability
layers, single-source-of-truth pointers so the constitution never bloats, a
two-tier global/local capability model, a built-in self-consistency check, and a
reflection loop that compounds. You answer a short interview; you get a project
that knows how to run and improve itself.

## The eight design philosophies

1. **The main agent is a CEO, not a worker** — orchestrate, don't do manual labor.
2. **Everything is a sub-agent; concurrency is the default** — fan out at the seams.
3. **Four-layer architecture** — CEO → sub-agent prompt → skill → MCP/CLI; pointers down, details never up.
4. **Single source of truth + pointers** — the constitution stays lean forever.
5. **Global vs local capabilities** — reuse what's shared, build only what's unique.
6. **Reflection is always the last step** — two loops: per-cycle + cross-session; event-triggered so it survives disuse, not "remember to."
7. **Constitution-as-code** — a fail-closed `doctor` command, wired to a change-event, keeps the docs' claims equal to filesystem reality.
8. **A standard project shape** — root constitution + `documentation/` from birth.

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
skill runs the interview and generates the constitution skeleton.

> Filenames like `CLAUDE.md` are conventions — substitute whatever your runtime
> reads as top-level agent instructions (e.g. `AGENTS.md`). The ideas are
> runtime-agnostic.

## What's in this repo

| Path | What it is |
|---|---|
| [`SKILL.md`](./SKILL.md) | The meta system prompt: philosophies + interview protocol + generation rules. The main artifact. |
| [`templates/CLAUDE.md.template`](./templates/CLAUDE.md.template) | The fill-in constitution skeleton (Rules **and** Don'ts). |
| [`templates/configuration.json.template`](./templates/configuration.json.template) | The brand single-source-of-truth. |
| [`templates/durable_self_iterate.md`](./templates/durable_self_iterate.md) | Reference recipe for Philosophies 6–7: a fail-closed `doctor` skeleton + the change-event trigger that fires the self-iterate loop. |
| [`CHANGELOG.md`](./CHANGELOG.md) | Version history (semver). |
| [`VERSION`](./VERSION) | Current version, for tooling / online upgrades. |

## What it does *not* do

It runs no business code, writes no application logic, and deploys nothing. It
only does **interview → generate the documentation skeleton + registries**. The
actual CLI, sub-agent prompts, and capability skills are grown later by the
project's own CEO + maintainer agent.

## Versioning & upgrades

Semantic versioning. The version is mirrored in `VERSION`, the `version:` field of
`SKILL.md` frontmatter, and `CHANGELOG.md`. To upgrade an installed copy, pull the
repo (or re-clone) and reload your skills. Watch releases for new versions.

## Contributing

Issues and PRs welcome — especially new project archetypes, sharper interview
questions, and template improvements. Keep the core principle intact: the
constitution stays lean; detail sinks into pointers.

## License

[MIT](./LICENSE) © 2026 preangelleo
