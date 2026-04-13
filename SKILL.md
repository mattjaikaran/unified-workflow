---
name: forge
version: 0.2.0
description: "Forge: a Claude Code skill that forges GSD (project management) and Superpowers (engineering discipline) into one coherent workflow. Prevents double-planning, conflicting subagent models, and overlapping design capture by providing a single routing decision at task start."
---

# Forge — GSD + Superpowers, United

## Core Rule

Check for `.planning/` directory in the project root. This single check determines routing.

## Primary Routing Table

| Scenario | `.planning/` exists? | System | Entry Point |
|----------|---------------------|--------|-------------|
| New project (greenfield) | No → creates it | GSD | `/gsd:new-project` |
| Brownfield onboarding | No → creates it | GSD | `/gsd:map-codebase` then `/gsd:new-project` |
| New feature in GSD project | Yes | GSD | `/gsd:discuss-phase` → `/gsd:plan-phase` → `/gsd:execute-phase` |
| Quick task in GSD project | Yes | GSD | `/gsd:quick` |
| Trivial fix in GSD project | Yes | GSD | `/gsd:fast` (inline, no subagents) |
| Standalone feature (no GSD) | No | SP | **REQUIRED:** `brainstorming` → `writing-plans` → `subagent-driven-development` |
| Bug fix (any context) | Either | SP first | **REQUIRED:** `systematic-debugging` ALWAYS first, then route fix through appropriate system |
| Code review | Either | SP | **REQUIRED:** `requesting-code-review` (fills GSD's missing review step) |
| Resume work | Yes | GSD | `/gsd:resume-work` |
| Resume work | No | SP | Re-read plan files in `docs/plans/` |

## Extended Routing (GSD 1.34+)

| Scenario | Entry Point | Notes |
|----------|-------------|-------|
| UI/frontend phase | `/gsd:ui-phase` → `/gsd:ui-review` | Design contract (UI-SPEC.md) before implementation |
| Security audit | `/gsd:secure-phase` | Verify threat mitigations post-implementation |
| Codebase intelligence | `/gsd:intel` | Query, refresh, or inspect `.planning/intel/` |
| Code review + auto-fix | `/gsd:code-review` → `/gsd:code-review-fix` | Two-step: find issues, then fix atomically |
| Autonomous execution | `/gsd:autonomous` | Runs all remaining phases: discuss → plan → execute |
| Parallel workstreams | `/gsd:workstreams` | Manage concurrent work across features |
| Persistent debugging | `/gsd:debug` | 4-phase debugging with knowledge base persistence |
| Phase assumptions | `/gsd:list-phase-assumptions` | Surface Claude's assumptions before planning |
| Milestone audit | `/gsd:audit-milestone` → `/gsd:plan-milestone-gaps` | Verify intent delivered, fill gaps |
| Project statistics | `/gsd:stats` | Phases, plans, git metrics, timeline |
| Session report | `/gsd:session-report` | Token usage estimates and work summary |

## Two-Layer Model

- **GSD = macro layer**: project structure, milestones, phases, plans, state tracking, pause/resume
- **Superpowers = micro layer**: TDD, debugging methodology, verification, code review per task

GSD orchestrates WHAT to build and in what order. Superpowers enforce HOW to build each piece correctly.

## Forge Commands

| Command | Purpose |
|---------|---------|
| `/forge:status` | Where am I? Single view across GSD + SP state |
| `/forge:check` | Verify GSD + SP installation and detect misconfigurations |
| `/forge:next` | Smart next-action — reads state, suggests one command |
| `/forge:migrate` | Upgrade path to [Meridian](https://github.com/mattjaikaran/meridian) |

## Integration Rules (Summary)

1. **Plan location**: GSD uses `.planning/phases/`, SP uses `docs/plans/`. Never mix.
2. **Brainstorming**: Use `discuss-phase` for GSD work, `brainstorming` for standalone only. Never both.
3. **Subagent execution**: GSD `execute-phase` OR SP `subagent-driven-development`. Never nested.
4. **Debugging**: SP `systematic-debugging` runs first for all bugs. GSD deviation Rule 1 covers only incidental executor bugs.
5. **Code review after GSD**: Run `requesting-code-review` after GSD verify-work, before merge.
6. **UI discipline**: For frontend phases, run `/gsd:ui-phase` to produce UI-SPEC.md before implementation, then `/gsd:ui-review` after.
7. **Learnings persist**: GSD captures execution patterns automatically. Review with `/gsd:intel` to inform future phases.

## Anti-Patterns (Never Do These)

- Run `brainstorming` inside a GSD phase (use `discuss-phase`)
- Create `docs/plans/` in a GSD project (use `.planning/phases/`)
- Nest SP `subagent-driven-development` inside GSD `execute-phase`
- Skip `systematic-debugging` for bugs in GSD projects
- Create `.planning/` manually (use `/gsd:new-project`)
- Use `/gsd:quick` for multi-phase work (use full pipeline)
- Merge without code review (GSD has no review step built in)
- Run both `discuss-phase` AND `brainstorming` (pick one based on `.planning/` existence)

## Complexity Escalation

```
Trivial (typo, config) → /gsd:fast
Small (< 30 min, < 5 files) → /gsd:quick
Medium (single phase) → discuss → plan → execute
Large (multi-phase) → new-project or add-phase pipeline
Autonomous (hands-off) → /gsd:autonomous
```

For detailed workflows and conflict resolution, load files from `references/`. Workflow guides are in `references/workflows.md`.
