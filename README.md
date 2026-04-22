# Torque

> **v0.3.0** — A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that converts [GSD](https://github.com/cline/gsd) + [Superpowers](https://github.com/obra/superpowers) into one coherent workflow

One skill that makes two powerful systems work as one. No database, no config, no overhead — just intelligent routing.

## The Problem

GSD (project management) and Superpowers (engineering discipline) are excellent independently, but without coordination they conflict:

- **Double-planning**: `brainstorming` runs inside a GSD phase, creating competing design docs
- **Wrong plan location**: SP writes to `docs/plans/`, GSD executors look in `.planning/phases/`
- **Subagent nesting**: SP's `subagent-driven-development` spawns inside GSD's `execute-phase`, burning 400k tokens
- **Missing review**: GSD has no code review step — phases ship without quality checks
- **Wrong tool for the job**: `systematic-debugging` skipped in GSD projects, `/gsd:quick` used for multi-phase work

## The Solution

One routing decision based on one question: **does `.planning/` exist?**

```
START: "I need to do development work"
  │
  ├─ Bug fix?           → systematic-debugging FIRST, then route
  ├─ Code review?       → requesting-code-review (send) / receiving-code-review (receive)
  ├─ Uncertain approach? → /gsd:spike (validate before committing)
  ├─ Need a PRD?        → write-a-prd → prd-to-issues → route to GSD/SP
  │
  ├─ .planning/ exists? (GSD project)
  │   ├─ Trivial fix      → /gsd:fast
  │   ├─ Small task        → /gsd:quick
  │   ├─ Feature work      → discuss → spec → plan → execute
  │   ├─ AI/LLM phase     → /gsd:ai-integration-phase → plan → execute → eval-review
  │   ├─ UI/frontend       → /gsd:ui-phase → implement → /gsd:ui-review
  │   ├─ Resume work       → /gsd:resume-work
  │   ├─ Phase complete    → verify → code review → /gsd:ship
  │   └─ Capture idea      → /gsd:note / /gsd:add-backlog / /gsd:plant-seed
  │
  └─ No .planning/? (standalone)
      ├─ New project       → /gsd:new-project
      ├─ Existing codebase → /gsd:map-codebase
      └─ Feature work      → brainstorming → writing-plans → subagent-driven-dev
```

## Two-Layer Model

| Layer | System | Responsibility |
|-------|--------|---------------|
| **Macro** | GSD | Project → milestones → phases → plans → state |
| **Micro** | Superpowers | TDD, debugging, verification, code review per task |

GSD orchestrates *what* to build. Superpowers enforce *how* to build it correctly.

## Commands

| Command | Purpose |
|---------|---------|
| `/torque:status` | Where am I? Unified view across GSD + SP |
| `/torque:check` | Verify installation and detect misconfigurations |
| `/torque:next` | Smart next-action — reads state, suggests one command |
| `/torque:migrate` | Assess upgrade path to Meridian |

## What's New in v0.3

- **Full GSD + SP coverage** — routes 40+ GSD commands and 15+ Superpowers skills
- **AI/LLM integration path** — `/gsd:ai-integration-phase` → AI-SPEC.md → eval pipeline
- **Spec phase in pipeline** — `/gsd:spec-phase` locks falsifiable requirements before planning
- **Spike/sketch exploration** — validate feasibility before committing to full planning
- **PRD pipeline** — `write-a-prd` → `prd-to-issues` → route to execution
- **Ship flow** — `/gsd:ship` and `/gsd:pr-branch` for clean PRs (filters `.planning/`)
- **Review both ways** — `requesting-code-review` + `receiving-code-review`
- **12 integration rules** (was 7), **15 workflow guides** (was 10), **10 conflict scenarios** (was 7)
- **Extended utilities** — backlog, todos, spikes, architecture analysis, changelog, browser testing

See [CHANGELOG.md](CHANGELOG.md) for full details.

## Install

```bash
git clone https://github.com/mattjaikaran/torque.git ~/.claude/skills/torque
```

Verify:
```bash
ls ~/.claude/skills/torque/SKILL.md && echo "Installed"
```

## Prerequisites

- [GSD](https://github.com/cline/gsd) (v1.34+ recommended)
- [Superpowers](https://github.com/obra/superpowers)

Run `/torque:check` after install to verify everything is wired up.

## Structure

```
torque/
├── SKILL.md                              # Main routing logic + version
├── CHANGELOG.md                          # Version history
├── skills/
│   ├── status/SKILL.md                   # /torque:status
│   ├── check/SKILL.md                    # /torque:check
│   ├── next/SKILL.md                     # /torque:next
│   └── migrate/SKILL.md                  # /torque:migrate
└── references/
    ├── decision-flowchart.md             # Visual decision tree + anti-patterns
    ├── integration-rules.md              # GSD executor + SP discipline protocols
    ├── workflows.md                      # 10 step-by-step scenario guides
    ├── conflict-resolution.md            # Troubleshooting guide
    └── comparison.md                     # Torque vs Meridian vs raw
```

## Key Integration Rules

1. **Plan location**: GSD uses `.planning/phases/`, SP uses `docs/plans/`. Never mix.
2. **Brainstorming**: `discuss-phase` for GSD work, `brainstorming` for standalone only.
3. **Subagent execution**: GSD executors OR SP subagent-driven-dev. Never nested.
4. **Debugging**: SP `systematic-debugging` runs first for all bugs.
5. **Code review (send)**: Always run after GSD `verify-work` — fills GSD's missing review step.
6. **Code review (receive)**: Use `receiving-code-review` to verify feedback before implementing.
7. **UI discipline**: Run `/gsd:ui-phase` before frontend implementation, `/gsd:ui-review` after.
8. **AI discipline**: Run `/gsd:ai-integration-phase` before AI/LLM work, `/gsd:eval-review` after.
9. **Spec before plan**: Run `/gsd:spec-phase` between discuss and plan for ambiguous scope.
10. **Spike before commit**: Run `/gsd:spike` when feasibility is uncertain.
11. **Ship cleanly**: Use `/gsd:ship` or `/gsd:pr-branch` to filter `.planning/` from PRs.
12. **Learnings persist**: GSD captures execution patterns. Query with `/gsd:intel`.

## Torque vs Meridian

| | Torque | [Meridian](https://github.com/mattjaikaran/meridian) |
|--|-------|----------|
| **Approach** | Routes between GSD + SP | Standalone engine |
| **Setup** | `git clone` and go | `git clone` + `/meridian:init` |
| **State** | File-based (`.planning/`) | SQLite database |
| **Dependencies** | GSD + Superpowers | None |
| **Commands** | 4 + routes to GSD/SP | 39 native |
| **Resume** | File-based | Deterministic (same state = same prompt) |
| **Quality gates** | Suggested | Enforced |
| **Board sync** | No | Yes (Linear, Jira) |
| **Remote dispatch** | No | Yes |
| **Best for** | Simple-to-medium projects | Complex, long-running projects |

**Start here. Upgrade when you need to.** Run `/torque:migrate` to assess.

## License

MIT
