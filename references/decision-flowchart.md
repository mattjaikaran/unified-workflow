# Decision Flowchart

## Quick Reference

```
START: "I need to do development work"
  │
  ├─ Is this a bug fix?
  │   └─ YES → systematic-debugging FIRST
  │       │   Then route the fix:
  │       │   ├─ .planning/ exists → /gsd:quick or /gsd:execute-phase
  │       │   └─ No .planning/    → SP subagent-driven-dev
  │       └─ NO ↓
  │
  ├─ Is this a code review?
  │   └─ YES → requesting-code-review (always SP)
  │       └─ NO ↓
  │
  ├─ Does .planning/ exist in this project?
  │   ├─ YES (GSD project) ↓
  │   │   ├─ Resuming previous work?   → /gsd:resume-work
  │   │   ├─ Trivial fix (typo/config)?→ /gsd:fast
  │   │   ├─ Quick/small task?         → /gsd:quick
  │   │   ├─ UI/frontend phase?        → /gsd:ui-phase → implement → /gsd:ui-review
  │   │   ├─ Security audit needed?    → /gsd:secure-phase
  │   │   ├─ New feature/phase work?   → /gsd:discuss-phase → plan → execute
  │   │   ├─ Run everything hands-off? → /gsd:autonomous
  │   │   ├─ Parallel features?        → /gsd:workstreams
  │   │   ├─ Phase complete?           → /gsd:verify-work → requesting-code-review
  │   │   └─ Milestone complete?       → /gsd:audit-milestone → /gsd:complete-milestone
  │   │
  │   └─ NO (standalone work) ↓
  │       ├─ Brand new project?             → /gsd:new-project (creates .planning/)
  │       ├─ Existing codebase, first time? → /gsd:map-codebase → /gsd:new-project
  │       ├─ Feature work?                  → brainstorming → writing-plans → subagent-driven-dev
  │       └─ Resuming standalone?           → Re-read docs/plans/, pick up where left off
  │
  ├─ Not sure what to do?
  │   └─ /forge:next (reads state, suggests one action)
  │
  └─ END
```

## Complexity Escalation

```
How big is this task?
  │
  ├─ Trivial (typo, config change, one-liner)
  │   └─ /gsd:fast — inline, no subagents, no planning
  │
  ├─ Small (< 30 min, < 5 files)
  │   └─ /gsd:quick — atomic commits, optional state tracking
  │
  ├─ Medium (single phase, clear scope)
  │   └─ /gsd:discuss-phase → /gsd:plan-phase → /gsd:execute-phase
  │
  ├─ Large (multi-phase, architectural decisions)
  │   └─ /gsd:new-project or /gsd:add-phase pipeline
  │
  └─ Autonomous (hands-off execution of remaining phases)
      └─ /gsd:autonomous — discuss → plan → execute per phase
```

## Decision Table (Scenario → Action)

| Scenario | Route | Entry Skill | Plan Location | Subagent Model |
|----------|-------|-------------|---------------|----------------|
| Greenfield project | GSD | `/gsd:new-project` | `.planning/phases/` | GSD fresh-context executors |
| Brownfield onboarding | GSD | `/gsd:map-codebase` | `.planning/phases/` | GSD mapper agents |
| Feature in GSD project | GSD | `/gsd:discuss-phase` | `.planning/phases/` | GSD fresh-context executors |
| Quick task in GSD project | GSD | `/gsd:quick` | `.planning/phases/` | Inline (no subagent) |
| Trivial fix in GSD project | GSD | `/gsd:fast` | None | Inline (no subagent) |
| UI/frontend phase | GSD | `/gsd:ui-phase` | `.planning/phases/` | GSD executors + UI-SPEC.md |
| Security audit | GSD | `/gsd:secure-phase` | `.planning/phases/` | Security auditor agent |
| Autonomous execution | GSD | `/gsd:autonomous` | `.planning/phases/` | All phases sequentially |
| Parallel workstreams | GSD | `/gsd:workstreams` | `.planning/phases/` | Per-workstream executors |
| Standalone feature | SP | `brainstorming` | `docs/plans/` | SP per-task + 2-stage review |
| Bug fix (any) | SP first | `systematic-debugging` | N/A (inline fix) | Depends on project type |
| Code review (any) | SP | `requesting-code-review` | N/A | Inline |
| Resume (GSD) | GSD | `/gsd:resume-work` | `.planning/phases/` | Restores state |
| Resume (standalone) | SP | Read `docs/plans/` | `docs/plans/` | Manual |
| End of phase | Both | `/gsd:verify-work` + `requesting-code-review` | `.planning/phases/` | GSD verifier + SP reviewer |
| End of milestone | GSD | `/gsd:audit-milestone` + `/gsd:complete-milestone` | `.planning/` | Milestone auditor |

## Extended GSD Commands (1.34+)

| Need | Command | When to Use |
|------|---------|-------------|
| Project intelligence | `/gsd:intel` | Query codebase knowledge before planning |
| Phase assumptions | `/gsd:list-phase-assumptions` | See Claude's approach before committing |
| Code review + fix | `/gsd:code-review` → `/gsd:code-review-fix` | Automated review with atomic fixes |
| Milestone gaps | `/gsd:plan-milestone-gaps` | After audit finds incomplete work |
| Project stats | `/gsd:stats` | Phases, plans, git metrics, timeline |
| Session report | `/gsd:session-report` | Token usage and work summary |
| Persistent debug | `/gsd:debug` | Complex bugs needing session persistence |
| Notes/seeds | `/gsd:note`, `/gsd:plant-seed` | Capture ideas for future phases |

## Anti-Patterns

| Anti-Pattern | Why It's Wrong | Correct Approach |
|-------------|----------------|------------------|
| `brainstorming` inside GSD phase | Creates competing design docs, confuses plan location | Use `/gsd:discuss-phase` which feeds CONTEXT.md to downstream GSD agents |
| `writing-plans` in GSD project | Plan goes to `docs/plans/`, GSD executors won't find it | Use `/gsd:plan-phase` which writes to `.planning/phases/` |
| SP `subagent-driven-dev` nested in GSD `execute-phase` | Double subagent layer, context rot, competing state | GSD executors already handle per-plan execution with fresh 200k context |
| Skipping `systematic-debugging` in GSD | GSD deviation Rule 1 is for incidental bugs, not root cause analysis | Always run systematic-debugging first, then route the fix |
| Creating `.planning/` manually | Missing PROJECT.md, ROADMAP.md, proper phase structure | Use `/gsd:new-project` to initialize correctly |
| Using `/gsd:quick` for multi-phase work | Skips planning, no state tracking for complex work | Use full `/gsd:discuss-phase` → `plan-phase` → `execute-phase` pipeline |
| Merging without code review | GSD has no review step built in | Always run `requesting-code-review` after `/gsd:verify-work` |
| Running both `discuss-phase` and `brainstorming` | Double design capture, conflicting outputs | Pick ONE based on `.planning/` existence |
| Skipping UI-SPEC.md for frontend | Implementing UI without design contract | Run `/gsd:ui-phase` first to produce UI-SPEC.md |
