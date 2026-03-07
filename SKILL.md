---
name: unified-workflow
description: Use when starting any development work to route between GSD (project management) and Superpowers (engineering discipline) systems. Prevents double-planning, conflicting subagent models, and overlapping design capture by providing a single routing decision at task start.
---

# Unified Workflow Router

## Core Rule

Check for `.planning/` directory in the project root. This single check determines routing.

## Routing Table

| Scenario | `.planning/` exists? | System | Entry Point |
|----------|---------------------|--------|-------------|
| New project (greenfield) | No → creates it | GSD | `/gsd:new-project` |
| Brownfield onboarding | No → creates it | GSD | `/gsd:map-codebase` then `/gsd:new-project` |
| New feature in GSD project | Yes | GSD | `/gsd:discuss-phase` → `/gsd:plan-phase` → `/gsd:execute-phase` |
| Quick task in GSD project | Yes | GSD | `/gsd:quick` |
| Standalone feature (no GSD) | No | SP | **REQUIRED SUB-SKILL:** `brainstorming` → `writing-plans` → `subagent-driven-development` |
| Bug fix (any context) | Either | SP first | **REQUIRED SUB-SKILL:** `systematic-debugging` ALWAYS first, then route fix through appropriate system |
| Code review | Either | SP | **REQUIRED SUB-SKILL:** `requesting-code-review` (GSD has no review step) |
| Resume work | Yes | GSD | `/gsd:resume-work` |
| Resume work | No | SP | Re-read plan files in `docs/plans/` |

## Two-Layer Model

- **GSD = macro layer**: project structure, milestones, phases, plans, state tracking, pause/resume
- **Superpowers = micro layer**: TDD, debugging methodology, verification, code review per task

GSD orchestrates WHAT to build and in what order. Superpowers enforce HOW to build each piece correctly.

## Integration Rules (Summary)

1. **Plan location**: GSD uses `.planning/phases/`, SP uses `docs/plans/`. Never mix.
2. **Brainstorming**: Use `discuss-phase` for GSD work, `brainstorming` for standalone only. Never both.
3. **Subagent execution**: GSD `execute-phase` OR SP `subagent-driven-development`. Never nested.
4. **Debugging**: SP `systematic-debugging` runs first for all bugs. GSD deviation Rule 1 covers only incidental executor bugs.
5. **Code review after GSD**: Run **REQUIRED SUB-SKILL:** `requesting-code-review` after GSD verify-work, before merge.

## Anti-Patterns (Never Do These)

- Run `brainstorming` inside a GSD phase (use `discuss-phase`)
- Create `docs/plans/` in a GSD project (use `.planning/phases/`)
- Nest SP `subagent-driven-development` inside GSD `execute-phase`
- Skip `systematic-debugging` for bugs in GSD projects

For detailed workflows and conflict resolution, load files from `references/`.
