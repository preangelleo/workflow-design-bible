# Changelog

All notable changes to the **Workflow Design Bible** are documented here.
This project follows [Semantic Versioning](https://semver.org/).

The skill version is mirrored in three places that must stay in sync:
`VERSION`, the `version:` field in `SKILL.md` frontmatter, and the top entry here.

## [1.1.0] — 2026-06-19

### Added
- **`templates/durable_self_iterate.md`** — reference recipe for Philosophies 6–7:
  a generic, language-agnostic **fail-closed `doctor`** skeleton plus the
  **change-event trigger** (pre-commit / file-watch) that fires the self-iterate
  loop, so reflection survives disuse instead of depending on being remembered.

### Changed
- **Philosophy 6** now names the durability failure explicitly and prescribes the
  two mechanics that fix it: trigger on the change event (not on memory), and
  converge the loop on the deterministic `doctor` anchor.
- **Philosophy 7** sharpens `doctor` to **fail-closed** and **wired to a trigger**
  (the most common failure being a `doctor` that exists but is never invoked).
- **Generation rule 5** now **scaffolds** the durability loop (recipe + a
  fail-closed `doctor` stub + one change-event trigger) instead of merely
  *recommending* a `doctor` for later.
- **`CLAUDE.md.template`** carries the event-trigger + fail-closed-`doctor` rule in
  the reflection-loops note and the propagation-consistency standing rule.
- **Quality self-check (§F)** gates on the `doctor` being scaffolded + fail-closed
  and the loop being event-triggered.

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
