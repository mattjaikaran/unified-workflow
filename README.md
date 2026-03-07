# unified-workflow

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that routes between **GSD** (project management) and **Superpowers** (engineering discipline) systems, making them work as one coherent workflow.

## Problem

GSD and Superpowers operate at different layers and complement each other, but without guidance they conflict — double-planning, competing subagent models, overlapping design capture.

## Solution

A single routing decision at task start based on one question: **does `.planning/` exist?**

| Scenario | System | Entry Point |
|----------|--------|-------------|
| New project (greenfield) | GSD | `/gsd:new-project` |
| Brownfield onboarding | GSD | `/gsd:map-codebase` → `/gsd:new-project` |
| Feature in active GSD project | GSD | `/gsd:discuss-phase` → `plan` → `execute` |
| Quick task in GSD project | GSD | `/gsd:quick` |
| Standalone feature (no GSD) | SP | `brainstorming` → `writing-plans` → `subagent-driven-dev` |
| Bug fix (any context) | SP first | `systematic-debugging` always first |
| Code review | SP | `requesting-code-review` (GSD has no review) |
| Resume work | Either | `.planning/` exists → GSD, otherwise SP |

## Two-Layer Model

- **GSD = macro layer** — project → milestones → phases → plans → state
- **Superpowers = micro layer** — TDD, debugging, verification, code review per task

GSD orchestrates *what* to build. Superpowers enforce *how* to build it correctly.

## Key Integration Rules

1. **Plan location**: GSD uses `.planning/phases/`, SP uses `docs/plans/`. Never mix.
2. **Brainstorming**: `discuss-phase` for GSD work, `brainstorming` for standalone only.
3. **Subagent execution**: GSD executors OR SP subagent-driven-dev. Never nested.
4. **Debugging**: SP `systematic-debugging` runs first for all bugs.
5. **Code review**: Always run after GSD `verify-work` — fills GSD's missing review step.

## Install

```bash
# Clone into your skills directory
git clone https://github.com/mattjaikaran/unified-workflow.git ~/.claude/skills/unified-workflow
```

## Structure

```
unified-workflow/
├── SKILL.md                              # Routing logic (<500 words)
└── references/
    ├── decision-flowchart.md             # Visual decision tree + anti-patterns
    ├── integration-rules.md              # GSD executor + SP discipline protocols
    └── unified-workflows.md              # 7 step-by-step scenario guides
```

## Prerequisites

- [Superpowers](https://github.com/nicobailon/claude-code-superpowers) skills installed
- [GSD](https://github.com/cline/gsd) skills installed

## License

MIT
