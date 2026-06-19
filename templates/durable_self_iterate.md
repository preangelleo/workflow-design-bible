# Durable self-iterate: make the reflection loop survive disuse

> **Reference recipe for Philosophies 6 & 7.** Drop a tuned copy of this file into
> your generated project's `documentation/`. It turns "reflection is always the
> last step" from a *remembered habit* into a *mechanism that fires on change and
> converges on a deterministic check* — so the loop keeps running even when nobody
> remembers to run it.

## The failure this prevents

A self-improving project stops improving the moment its improvement step becomes
optional. "The agent should reflect at the end of each cycle" is prose; prose is
the first thing dropped under deadline pressure. Within a few cycles the
reflection loop has quietly fallen into disuse, the docs drift from reality, and
nobody notices until something breaks. **A loop that depends on being remembered
is not a loop.**

Two mechanics fix it, and they reinforce each other:

1. **Trigger on the event, not on memory** — bind the loop to a *change*, so it
   runs because something happened, not because someone recalled to run it.
2. **Converge on a deterministic anchor (`doctor`), fail-closed** — end the loop by
   re-asserting a machine-checkable invariant that *blocks "done"* on drift, so
   reflection compounds toward a green check instead of eroding into vibes.

---

## Part 1 · The `doctor` — a fail-closed consistency lint

`doctor` answers one question deterministically: **do the project's own claims
match filesystem reality?** The constitution says "9 sub-agents, 7 skills, a
`build` and a `publish` subcommand"; `doctor` counts what actually exists and
**exits non-zero on any mismatch**. Non-zero is a *gate*, not a warning — an agent
can argue past a printed warning, it cannot argue past a failed exit code.

Generic, language-agnostic skeleton (pseudocode — port to whatever your CLI is in):

```
# doctor: claims (from docs/registry) == reality (from filesystem). exit 1 on drift.

drift = []

# 1. Registry vs filesystem: every claimed capability must exist, and vice versa.
claimed_agents  = parse_table(CONSTITUTION, "Sub-agent registry")     # names the docs promise
actual_agents   = list_files(".claude/agents/*.md")                   # what's on disk
drift += symmetric_diff(claimed_agents, actual_agents)                # missing OR undeclared

claimed_skills  = parse_table(CONSTITUTION, "Skill index")
actual_skills   = list_dirs(".claude/skills/*/SKILL.md")
drift += symmetric_diff(claimed_skills, actual_skills)

# 2. Contract checks: the things the docs assert about each artifact.
for skill in actual_skills:
    if not declares_built_from(skill):        # Philosophy 3: every skill names its MCP/CLI
        drift.append(f"{skill}: missing 'built from' declaration")

claimed_cmds = parse_subcommands(CONSTITUTION)        # subcommands the docs reference
actual_cmds  = introspect_cli_subcommands()           # `<core> --help` / arg-parser registry
drift += symmetric_diff(claimed_cmds, actual_cmds)

# 3. Counts the prose asserts (e.g. "9 sub-agents") must match reality.
for (claimed_n, actual_n, label) in declared_counts(CONSTITUTION):
    if claimed_n != actual_n:
        drift.append(f"{label}: doc says {claimed_n}, filesystem has {actual_n}")

# 4. Structural invariants from Philosophy 8 (standard shape).
for required in ["documentation/", "reflections/", "reports/", "HANDOFF.md"]:
    if not exists(required):
        drift.append(f"standard shape: missing {required}")

if drift:
    print("doctor: project claims diverge from reality:")
    for d in drift: print("  -", d)
    exit(1)            # FAIL-CLOSED. this is the whole point.
print("doctor: claims == reality. ok.")
exit(0)
```

**Design rules for `doctor`:**
- **Deterministic and cheap.** No model calls on this path — pure parse + filesystem
  + introspection. It must be safe to run on every commit.
- **Symmetric.** Catch both *claimed-but-missing* and *exists-but-undeclared*. The
  second is how silent capabilities accumulate undocumented.
- **Fail-closed by default.** Exit non-zero on drift, always. Offer an explicit
  `--report` / `--json` mode for humans, but the default invocation gates.
- **Grow it incrementally.** A near-empty `doctor` that only checks the agent/skill
  counts on day one is still worth wiring up — because the *trigger* is in place,
  every new check you add starts enforcing itself immediately.

---

## Part 2 · The trigger — fire on change, not on memory

`doctor` is worthless if nothing invokes it. Bind it to the change event so it runs
*because the project changed*. Pick the primitive that fits where your project
lives; you usually want a commit-time gate plus, optionally, a live file-watch.

### A. Commit-time gate (most portable — a git pre-commit hook)

```bash
# .git/hooks/pre-commit   (or via a hooks manager: pre-commit, husky, lefthook)
#!/usr/bin/env bash
set -euo pipefail
# A leaf change must not land while the upper-layer contract is stale.
<core> doctor || {
  echo "pre-commit: doctor failed — docs/registry diverge from filesystem. Fix or update the constitution, then re-commit." >&2
  exit 1
}
```

A pre-push hook (or a CI status check that runs `doctor`) is the same idea one layer
out — use it as the team-visible / server-side backstop so the gate holds even if a
local hook is bypassed.

### B. Live file-watch (fires reflection as work lands, not just at commit)

Watch the dirs that signal a meaningful state transition (a build dir filling, a new
file in `reflections/`, an edited agent/skill) and fire on change. Cross-platform
options, pick one:

```bash
# fswatch (macOS/Linux) — fire doctor on any change under the watched paths
fswatch -o .claude/ documentation/ <core>/ | while read -r _; do
  <core> doctor || notify "doctor drift — reconcile before continuing"
done
```

```bash
# entr (lightweight, *nix) — re-run on change to tracked files
find .claude documentation -type f | entr -r <core> doctor
```

OS-native path-watch services work too — e.g. a `launchd` `WatchPaths` agent on
macOS, a `systemd` `path` unit on Linux, an editor/IDE save hook, or a CI job keyed
to the relevant paths. The principle is identical across all of them: **a real event
primitive fires the check; you do not shorten a polling interval to fake real-time.**

### C. Periodic fail-safe (the backstop, never the primary cadence)

A coarse scheduled run (e.g. once a day in CI, or the session-startup ritual calling
`doctor` before any new task) bounds worst-case staleness if every event trigger is
somehow missed. It is the seatbelt, not the steering — if `doctor` only ever runs on
a timer, you have rebuilt the "remember to reflect" problem with extra steps.

---

## Part 3 · Wiring it into the reflection step (closing Loop A)

With `doctor` and a trigger in place, the per-cycle reflection step gets a hard
terminal assertion instead of a soft retrospective. The reflection step is **done
only when `doctor` exits zero**:

```
Reflection & self-iterate (last step of every cycle):
  1. Review this cycle's flaws + efficiency; attribute root causes.
  2. Freeze any "still decided on the fly" decision into deterministic code
     (a CLI subcommand / template / SP edit) — dispatch the maintainer for the code.
  3. Propagation check: did this cycle's changes touch a leaf (CLI/skill/SP/template)?
     If so, re-confirm each upper layer's contract (the constitution's tables, the
     related documentation/).
  4. Run `<core> doctor`. Non-zero → the loop is NOT closed; reconcile and repeat.
     Zero → write the reflection to reflections/ and mark the cycle complete.
```

That final `doctor` assert is what makes the loop converge: reflection is no longer
"did we feel good about this cycle?" but "drive the project back to a green
deterministic check." The event trigger guarantees the step *runs*; the fail-closed
`doctor` guarantees it *means something*.

---

### TL;DR

| Symptom | Without this recipe | With it |
|---|---|---|
| Reflection step | "remember to do it" → dropped under pressure | fires on a change event |
| `doctor` | recommended for later, never built | scaffolded fail-closed from birth |
| Docs vs reality | drift, unnoticed | a leaf change can't land green while stale |
| "Done" | self-declared | gated by a non-zero exit code |
