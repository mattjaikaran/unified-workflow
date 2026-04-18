---
name: torque
version: 0.3.0
description: "Torque: a Claude Code skill that converts GSD (project management) and Superpowers (engineering discipline) into one coherent workflow. Prevents double-planning, conflicting subagent models, and overlapping design capture by providing a single routing decision at task start."
---

# Torque — GSD + Superpowers, United

## Core Rule

Check for `.planning/` directory in the project root. This single check determines routing.

## Primary Routing Table

| Scenario | `.planning/` exists? | System | Entry Point |
|----------|---------------------|--------|-------------|
| New project (greenfield) | No → creates it | GSD | `/gsd:new-project` |
| Brownfield onboarding | No → creates it | GSD | `/gsd:map-codebase` then `/gsd:new-project` |
| New feature in GSD project | Yes | GSD | `/gsd:discuss-phase` → `/gsd:spec-phase` → `/gsd:plan-phase` → `/gsd:execute-phase` |
| AI/LLM integration phase | Yes | GSD | `/gsd:ai-integration-phase` → `/gsd:plan-phase` → `/gsd:execute-phase` → `/gsd:eval-review` |
| Quick task in GSD project | Yes | GSD | `/gsd:quick` |
| Trivial fix in GSD project | Yes | GSD | `/gsd:fast` (inline, no subagents) |
| Standalone feature (no GSD) | No | SP | **REQUIRED:** `brainstorming` → `writing-plans` → `subagent-driven-development` |
| PRD-driven feature | Either | SP → GSD | `write-a-prd` → `prd-to-issues` → `/gsd:new-project` or `/gsd:add-phase` |
| Bug fix (any context) | Either | SP first | **REQUIRED:** `systematic-debugging` ALWAYS first, then route fix through appropriate system |
| Code review (send) | Either | SP | **REQUIRED:** `requesting-code-review` (fills GSD's missing review step) |
| Code review (receive) | Either | SP | `receiving-code-review` — verify feedback before implementing blindly |
| Stress-test a plan | Either | SP | `grill-me` — interview until all ambiguity is resolved |
| Architecture improvement | Either | SP | `improve-codebase-architecture` — surface friction, propose refactors as RFC issues |
| Ship / PR creation | Yes | GSD | `/gsd:ship` or `/gsd:pr-branch` (filters `.planning/` commits) |
| Ship (standalone) | No | SP | `finishing-a-development-branch` |
| Resume work | Yes | GSD | `/gsd:resume-work` |
| Resume work | No | SP | Re-read plan files in `docs/plans/` |

## Extended Routing (GSD 1.34+)

| Scenario | Entry Point | Notes |
|----------|-------------|-------|
| Spec refinement | `/gsd:spec-phase` | Socratic clarification of WHAT a phase delivers — produces SPEC.md before planning |
| AI/LLM design contract | `/gsd:ai-integration-phase` | Framework selection, implementation guidance, evaluation strategy → AI-SPEC.md |
| AI eval audit | `/gsd:eval-review` | Retroactive audit of AI phase evaluation coverage |
| Spike (feasibility test) | `/gsd:spike` → `/gsd:spike-wrap-up` | Throwaway experiment to validate approach before committing |
| Sketch (UI mockup) | `/gsd:sketch` → `/gsd:sketch-wrap-up` | Rapid HTML mockups with multi-variant exploration |
| UI/frontend phase | `/gsd:ui-phase` → `/gsd:ui-review` | Design contract (UI-SPEC.md) before implementation |
| Security audit | `/gsd:secure-phase` | Verify threat mitigations post-implementation |
| Codebase intelligence | `/gsd:intel` | Query, refresh, or inspect `.planning/intel/` |
| Code review + auto-fix | `/gsd:code-review` → `/gsd:code-review-fix` | Two-step: find issues, then fix atomically |
| Autonomous execution | `/gsd:autonomous` | Runs all remaining phases: discuss → plan → execute |
| Parallel workstreams | `/gsd:workstreams` | Manage concurrent work across features |
| Persistent debugging | `/gsd:debug` | 4-phase debugging with knowledge base persistence |
| Phase assumptions | `/gsd:list-phase-assumptions` | Surface Claude's assumptions before planning |
| Nyquist validation | `/gsd:validate-phase` | Audit and fill validation gaps for a completed phase |
| Milestone audit | `/gsd:audit-milestone` → `/gsd:plan-milestone-gaps` | Verify intent delivered, fill gaps |
| Ship / PR creation | `/gsd:ship` | Create PR, run review, prepare for merge |
| Clean PR branch | `/gsd:pr-branch` | Filter out `.planning/` commits for clean code review |
| Safe revert | `/gsd:undo` | Roll back phase or plan commits using phase manifest |
| Project statistics | `/gsd:stats` | Phases, plans, git metrics, timeline |
| Session report | `/gsd:session-report` | Token usage estimates and work summary |
| Docs generation | `/gsd:docs-update` | Generate/update project docs verified against codebase |
| Changelog | `changelog-generator` (SP) | Auto-generate user-facing changelogs from git commits |
| Diagnostics | `/gsd:health` | Diagnose and repair `.planning/` directory issues |
| Post-mortem | `/gsd:forensics` | Investigate failed GSD workflows |
| Learnings extraction | `/gsd:extract_learnings` | Extract decisions, patterns, surprises from completed phases |

## Idea Capture & Backlog

| Scenario | Entry Point | Notes |
|----------|-------------|-------|
| Quick note | `/gsd:note` | Zero-friction idea capture — append, list, or promote |
| Add todo | `/gsd:add-todo` | Capture task from conversation context |
| Check todos | `/gsd:check-todos` | List pending todos and select one to work |
| Backlog idea | `/gsd:add-backlog` | Park an idea in 999.x numbering |
| Review backlog | `/gsd:review-backlog` | Promote backlog items to active milestone |
| Plant seed | `/gsd:plant-seed` | Forward-looking idea with trigger conditions |
| Explore idea | `/gsd:explore` | Socratic ideation — think before committing |

## Browser & E2E Testing

| Scenario | Entry Point | Notes |
|----------|-------------|-------|
| Test web app | `webapp-testing` (SP) | Playwright-based: screenshots, form fills, link checks |
| Full browser automation | `playwright-skill` (SP) | Auto-detect dev servers, write test scripts |

## Two-Layer Model

- **GSD = macro layer**: project structure, milestones, phases, plans, state tracking, pause/resume
- **Superpowers = micro layer**: TDD, debugging methodology, verification, code review per task

GSD orchestrates WHAT to build and in what order. Superpowers enforce HOW to build each piece correctly.

## Torque Commands

| Command | Purpose |
|---------|---------|
| `/torque:status` | Where am I? Single view across GSD + SP state |
| `/torque:check` | Verify GSD + SP installation and detect misconfigurations |
| `/torque:next` | Smart next-action — reads state, suggests one command |
| `/torque:migrate` | Upgrade path to [Meridian](https://github.com/mattjaikaran/meridian) |

## Integration Rules (Summary)

1. **Plan location**: GSD uses `.planning/phases/`, SP uses `docs/plans/`. Never mix.
2. **Brainstorming**: Use `discuss-phase` for GSD work, `brainstorming` for standalone only. Never both.
3. **Subagent execution**: GSD `execute-phase` OR SP `subagent-driven-development`. Never nested.
4. **Debugging**: SP `systematic-debugging` runs first for all bugs. GSD deviation Rule 1 covers only incidental executor bugs.
5. **Code review after GSD**: Run `requesting-code-review` after GSD verify-work, before merge.
6. **Code review receiving**: When receiving review feedback, use `receiving-code-review` to verify suggestions before implementing blindly.
7. **UI discipline**: For frontend phases, run `/gsd:ui-phase` to produce UI-SPEC.md before implementation, then `/gsd:ui-review` after.
8. **AI discipline**: For AI/LLM phases, run `/gsd:ai-integration-phase` to produce AI-SPEC.md before implementation, then `/gsd:eval-review` after.
9. **Spec before plan**: For medium+ features, run `/gsd:spec-phase` between discuss and plan to lock falsifiable requirements.
10. **Spike before commit**: When feasibility is uncertain, run `/gsd:spike` before discuss-phase. Wrap findings with `/gsd:spike-wrap-up`.
11. **Ship cleanly**: Use `/gsd:ship` or `/gsd:pr-branch` to create PRs that exclude `.planning/` commits from code review.
12. **Learnings persist**: GSD captures execution patterns automatically. Review with `/gsd:intel` to inform future phases.

## Anti-Patterns (Never Do These)

- Run `brainstorming` inside a GSD phase (use `discuss-phase`)
- Create `docs/plans/` in a GSD project (use `.planning/phases/`)
- Nest SP `subagent-driven-development` inside GSD `execute-phase`
- Skip `systematic-debugging` for bugs in GSD projects
- Create `.planning/` manually (use `/gsd:new-project`)
- Use `/gsd:quick` for multi-phase work (use full pipeline)
- Merge without code review (GSD has no review step built in)
- Run both `discuss-phase` AND `brainstorming` (pick one based on `.planning/` existence)
- Implement review feedback without verifying it first (use `receiving-code-review`)
- Skip spec refinement for ambiguous phases (use `/gsd:spec-phase` between discuss and plan)
- Build AI features without evaluation strategy (use `/gsd:ai-integration-phase` first)
- Push `.planning/` commits in PRs (use `/gsd:pr-branch` to filter them out)
- Commit to a large approach without spiking first (use `/gsd:spike` for uncertain feasibility)

## Complexity Escalation

```
Trivial (typo, config) → /gsd:fast
Small (< 30 min, < 5 files) → /gsd:quick
Medium (single phase) → discuss → spec → plan → execute
Large (multi-phase) → new-project or add-phase pipeline
Uncertain feasibility → /gsd:spike first, then route by size
AI/LLM integration → /gsd:ai-integration-phase → plan → execute → eval-review
Autonomous (hands-off) → /gsd:autonomous
```

For detailed workflows and conflict resolution, load files from `references/`. Workflow guides are in `references/workflows.md`.
