# Decision Flowchart

## Quick Reference

```
START: "I need to do development work"
  │
  ├─ Is this a bug fix?
  │   └─ YES → systematic-debugging FIRST
  │       │   Then route the fix:
  │       │   ├─ .planning/ exists → GSD quick/execute
  │       │   └─ No .planning/    → SP subagent-driven-dev
  │       └─ NO ↓
  │
  ├─ Is this a code review?
  │   └─ YES → requesting-code-review (always SP)
  │       └─ NO ↓
  │
  ├─ Does .planning/ exist in this project?
  │   ├─ YES (GSD project) ↓
  │   │   ├─ Resuming previous work?  → /gsd:resume-work
  │   │   ├─ Quick/small task?        → /gsd:quick
  │   │   ├─ New feature/phase work?  → /gsd:discuss-phase → plan → execute
  │   │   └─ Phase complete?          → /gsd:verify-work → requesting-code-review
  │   │
  │   └─ NO (standalone work) ↓
  │       ├─ Brand new project?       → /gsd:new-project (creates .planning/)
  │       ├─ Existing codebase, first time? → /gsd:map-codebase → /gsd:new-project
  │       ├─ Feature work?            → brainstorming → writing-plans → subagent-driven-dev
  │       └─ Resuming standalone?     → Re-read docs/plans/, pick up where left off
  │
  └─ END
```

## Decision Table (Scenario → Action)

| Scenario | Route | Entry Skill | Plan Location | Subagent Model |
|----------|-------|-------------|---------------|----------------|
| Greenfield project | GSD | `/gsd:new-project` | `.planning/phases/` | GSD fresh-context executors |
| Brownfield onboarding | GSD | `/gsd:map-codebase` | `.planning/phases/` | GSD mapper agents |
| Feature in GSD project | GSD | `/gsd:discuss-phase` | `.planning/phases/` | GSD fresh-context executors |
| Quick task in GSD project | GSD | `/gsd:quick` | `.planning/phases/` | Inline (no subagent) |
| Standalone feature | SP | `brainstorming` | `docs/plans/` | SP per-task + 2-stage review |
| Bug fix (any) | SP first | `systematic-debugging` | N/A (inline fix) | Depends on project type |
| Code review (any) | SP | `requesting-code-review` | N/A | Inline |
| Resume (GSD) | GSD | `/gsd:resume-work` | `.planning/phases/` | Restores state |
| Resume (standalone) | SP | Read `docs/plans/` | `docs/plans/` | Manual |
| End of phase | Both | `/gsd:verify-work` + `requesting-code-review` | `.planning/phases/` | GSD verifier + SP reviewer |

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
