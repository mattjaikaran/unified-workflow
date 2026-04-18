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
  │   ├─ Sending review?   → requesting-code-review (always SP)
  │   └─ Receiving review? → receiving-code-review (verify before implementing)
  │       └─ NO ↓
  │
  ├─ Want to stress-test a plan/design?
  │   └─ YES → grill-me (interview until all ambiguity resolved)
  │       └─ NO ↓
  │
  ├─ Need a PRD / requirements doc?
  │   └─ YES → write-a-prd → prd-to-issues → route issues to GSD or SP
  │       └─ NO ↓
  │
  ├─ Uncertain if approach is feasible?
  │   └─ YES → /gsd:spike → /gsd:spike-wrap-up → then route by complexity
  │       └─ NO ↓
  │
  ├─ Does .planning/ exist in this project?
  │   ├─ YES (GSD project) ↓
  │   │   ├─ Resuming previous work?   → /gsd:resume-work
  │   │   ├─ Trivial fix (typo/config)?→ /gsd:fast
  │   │   ├─ Quick/small task?         → /gsd:quick
  │   │   ├─ UI/frontend phase?        → /gsd:ui-phase → implement → /gsd:ui-review
  │   │   ├─ AI/LLM integration?       → /gsd:ai-integration-phase → plan → execute → /gsd:eval-review
  │   │   ├─ Security audit needed?    → /gsd:secure-phase
  │   │   ├─ Need rapid UI mockup?     → /gsd:sketch → /gsd:sketch-wrap-up
  │   │   ├─ Ambiguous requirements?   → /gsd:discuss-phase → /gsd:spec-phase → plan → execute
  │   │   ├─ New feature/phase work?   → /gsd:discuss-phase → plan → execute
  │   │   ├─ Run everything hands-off? → /gsd:autonomous
  │   │   ├─ Parallel features?        → /gsd:workstreams
  │   │   ├─ Phase complete?           → /gsd:verify-work → requesting-code-review → /gsd:ship
  │   │   ├─ Milestone complete?       → /gsd:audit-milestone → /gsd:complete-milestone
  │   │   ├─ Architecture friction?    → improve-codebase-architecture
  │   │   └─ Capture an idea?          → /gsd:note or /gsd:add-backlog or /gsd:plant-seed
  │   │
  │   └─ NO (standalone work) ↓
  │       ├─ Brand new project?             → /gsd:new-project (creates .planning/)
  │       ├─ Existing codebase, first time? → /gsd:map-codebase → /gsd:new-project
  │       ├─ Feature work?                  → brainstorming → writing-plans → subagent-driven-dev
  │       ├─ Resuming standalone?           → Re-read docs/plans/, pick up where left off
  │       └─ Architecture friction?         → improve-codebase-architecture
  │
  ├─ Not sure what to do?
  │   └─ /torque:next (reads state, suggests one action)
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
  │   └─ /gsd:discuss-phase → /gsd:spec-phase → /gsd:plan-phase → /gsd:execute-phase
  │
  ├─ Medium (ambiguous scope)
  │   └─ /gsd:spike first → then route by result
  │
  ├─ Large (multi-phase, architectural decisions)
  │   └─ /gsd:new-project or /gsd:add-phase pipeline
  │
  ├─ AI/LLM integration
  │   └─ /gsd:ai-integration-phase → plan → execute → /gsd:eval-review
  │
  └─ Autonomous (hands-off execution of remaining phases)
      └─ /gsd:autonomous — discuss → plan → execute per phase
```

## Decision Table (Scenario → Action)

| Scenario | Route | Entry Skill | Plan Location | Subagent Model |
|----------|-------|-------------|---------------|----------------|
| Greenfield project | GSD | `/gsd:new-project` | `.planning/phases/` | GSD fresh-context executors |
| Brownfield onboarding | GSD | `/gsd:map-codebase` | `.planning/phases/` | GSD mapper agents |
| Feature in GSD project | GSD | `/gsd:discuss-phase` → `/gsd:spec-phase` | `.planning/phases/` | GSD fresh-context executors |
| AI/LLM integration | GSD | `/gsd:ai-integration-phase` | `.planning/phases/` | AI researcher + eval planner agents |
| PRD-driven feature | SP → GSD | `write-a-prd` → `prd-to-issues` | `.planning/phases/` or `docs/plans/` | Depends on project type |
| Feasibility spike | GSD | `/gsd:spike` | Throwaway | Inline experiment |
| UI mockup/sketch | GSD | `/gsd:sketch` | Throwaway | Multi-variant HTML |
| Quick task in GSD project | GSD | `/gsd:quick` | `.planning/phases/` | Inline (no subagent) |
| Trivial fix in GSD project | GSD | `/gsd:fast` | None | Inline (no subagent) |
| UI/frontend phase | GSD | `/gsd:ui-phase` | `.planning/phases/` | GSD executors + UI-SPEC.md |
| Security audit | GSD | `/gsd:secure-phase` | `.planning/phases/` | Security auditor agent |
| Autonomous execution | GSD | `/gsd:autonomous` | `.planning/phases/` | All phases sequentially |
| Parallel workstreams | GSD | `/gsd:workstreams` | `.planning/phases/` | Per-workstream executors |
| Standalone feature | SP | `brainstorming` | `docs/plans/` | SP per-task + 2-stage review |
| Bug fix (any) | SP first | `systematic-debugging` | N/A (inline fix) | Depends on project type |
| Code review (send) | SP | `requesting-code-review` | N/A | Inline |
| Code review (receive) | SP | `receiving-code-review` | N/A | Inline (verify first) |
| Stress-test plan | SP | `grill-me` | N/A | Interview loop |
| Architecture analysis | SP | `improve-codebase-architecture` | N/A | RFC issues |
| Resume (GSD) | GSD | `/gsd:resume-work` | `.planning/phases/` | Restores state |
| Resume (standalone) | SP | Read `docs/plans/` | `docs/plans/` | Manual |
| End of phase | Both | `/gsd:verify-work` + `requesting-code-review` | `.planning/phases/` | GSD verifier + SP reviewer |
| Ship / create PR | GSD | `/gsd:ship` or `/gsd:pr-branch` | `.planning/phases/` | Filters `.planning/` commits |
| End of milestone | GSD | `/gsd:audit-milestone` + `/gsd:complete-milestone` | `.planning/` | Milestone auditor |
| Idea capture | GSD | `/gsd:note`, `/gsd:add-backlog`, `/gsd:add-todo` | `.planning/` | Inline |

## Extended GSD Commands (1.34+)

| Need | Command | When to Use |
|------|---------|-------------|
| Spec refinement | `/gsd:spec-phase` | Lock falsifiable requirements between discuss and plan |
| AI design contract | `/gsd:ai-integration-phase` | Framework selection + eval strategy for AI/LLM phases |
| AI eval audit | `/gsd:eval-review` | Retroactive audit of AI phase evaluation coverage |
| Feasibility spike | `/gsd:spike` → `/gsd:spike-wrap-up` | Throwaway experiment before committing |
| UI sketch | `/gsd:sketch` → `/gsd:sketch-wrap-up` | Rapid HTML mockups with multi-variant exploration |
| Project intelligence | `/gsd:intel` | Query codebase knowledge before planning |
| Phase assumptions | `/gsd:list-phase-assumptions` | See Claude's approach before committing |
| Nyquist validation | `/gsd:validate-phase` | Audit and fill validation gaps post-phase |
| Code review + fix | `/gsd:code-review` → `/gsd:code-review-fix` | Automated review with atomic fixes |
| Ship / PR | `/gsd:ship` | Create PR, run review, prepare for merge |
| Clean PR branch | `/gsd:pr-branch` | Filter `.planning/` commits for clean review |
| Safe revert | `/gsd:undo` | Roll back phase/plan commits with dependency checks |
| Milestone gaps | `/gsd:plan-milestone-gaps` | After audit finds incomplete work |
| Project stats | `/gsd:stats` | Phases, plans, git metrics, timeline |
| Session report | `/gsd:session-report` | Token usage and work summary |
| Persistent debug | `/gsd:debug` | Complex bugs needing session persistence |
| Docs generation | `/gsd:docs-update` | Generate/update docs verified against codebase |
| Learnings | `/gsd:extract_learnings` | Extract decisions, patterns from completed phases |
| Health check | `/gsd:health` | Diagnose and repair `.planning/` issues |
| Post-mortem | `/gsd:forensics` | Investigate failed GSD workflows |
| Notes/seeds | `/gsd:note`, `/gsd:plant-seed` | Capture ideas for future phases |
| Todos | `/gsd:add-todo`, `/gsd:check-todos` | Task capture and tracking |
| Backlog | `/gsd:add-backlog`, `/gsd:review-backlog` | Park ideas, promote when ready |
| Explore idea | `/gsd:explore` | Socratic ideation before committing |

## Superpowers Extended Skills

| Need | Skill | When to Use |
|------|-------|-------------|
| PRD creation | `write-a-prd` | Structured requirements gathering via interview |
| PRD to issues | `prd-to-issues` | Break PRD into vertical-slice GitHub issues |
| Stress-test plan | `grill-me` | Interview loop until all ambiguity resolved |
| Architecture analysis | `improve-codebase-architecture` | Surface friction, propose refactors as RFC issues |
| Receiving review | `receiving-code-review` | Verify review feedback before implementing |
| Browser testing | `webapp-testing` or `playwright-skill` | Playwright-based E2E and visual testing |
| Changelog | `changelog-generator` | Auto-generate user-facing changelogs from commits |
| CI/CD | `ci-cd` | Pipeline design, optimization, DevSecOps scanning |
| Monitoring | `monitoring-observability` | Metrics, logs, traces, SLOs, dashboards |

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
| Skipping AI-SPEC.md for AI/LLM phases | No eval strategy, no framework guidance | Run `/gsd:ai-integration-phase` first to produce AI-SPEC.md |
| Implementing review feedback blindly | Reviewer may be wrong or lack context | Use `receiving-code-review` to verify suggestions first |
| Pushing `.planning/` commits in PRs | Noise in code review, exposes internal state | Use `/gsd:pr-branch` to create a clean branch |
| Committing to large approach without spiking | Wasted effort if approach is infeasible | Use `/gsd:spike` for uncertain feasibility first |
| Skipping spec for ambiguous phases | Vague requirements produce vague implementations | Run `/gsd:spec-phase` between discuss and plan |
