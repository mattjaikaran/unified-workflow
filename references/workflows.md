# Torque Workflow Guides

Step-by-step guides covering common development scenarios.

---

## 1. New Project (Greenfield)

**When**: Starting a project from scratch with no existing code.

**Steps:**
1. `/gsd:new-project` — deep context gathering, creates PROJECT.md and ROADMAP.md
2. `/gsd:discuss-phase` for Phase 1 — gather requirements, produce CONTEXT.md
3. `/gsd:plan-phase` for Phase 1 — create executable PLAN.md files
4. `/gsd:execute-phase` for Phase 1 — fresh-context executors build each plan
5. `/gsd:verify-work` — validate features match phase goals
6. `requesting-code-review` — review code quality
7. `finishing-a-development-branch` — merge/PR decision
8. Repeat steps 2-7 for subsequent phases

**Plan location**: `.planning/phases/1/`, `.planning/phases/2/`, etc.

---

## 2. New Feature in Active GSD Project

**When**: `.planning/` exists, adding a new feature that fits within the current milestone.

**Steps:**
1. Check `/gsd:progress` — understand current state and phase
2. If feature is a new phase: `/gsd:add-phase` or `/gsd:insert-phase`
3. `/gsd:discuss-phase` — gather context, produce CONTEXT.md
4. `/gsd:plan-phase` — create PLAN.md files with task breakdown
5. `/gsd:execute-phase` — build the feature
6. `/gsd:verify-work` — validate against phase goals
7. `requesting-code-review` — code quality check
8. `finishing-a-development-branch` — merge/PR

**Do NOT**: Run `brainstorming` or `writing-plans` (SP standalone skills). Use `discuss-phase` instead — it feeds CONTEXT.md to downstream GSD agents.

---

## 3. Bug Fix

**When**: A bug is reported or discovered, regardless of project type.

**Steps:**
1. `systematic-debugging` — ALWAYS first
   - Phase 1: Reproduce and characterize
   - Phase 2: Form hypotheses, rank by likelihood
   - Phase 3: Binary search / targeted investigation
   - Phase 4: Root cause fix and regression test
2. Route the fix based on project context:
   - **GSD project**: `/gsd:quick` for small fixes, or update current phase plan
   - **Standalone**: Apply fix directly, commit
3. `verification-before-completion` — prove the fix works

**For persistent/complex bugs in GSD projects**: Use `/gsd:debug` instead — it maintains debug state across sessions with a knowledge base.

**Do NOT**: Skip systematic-debugging even in GSD projects. GSD deviation Rule 1 ("auto-fix bugs") only covers incidental executor bugs, not user-reported issues.

---

## 4. Quick / Ad-Hoc Task

**When**: Small task that doesn't warrant full phase planning.

**Steps (GSD project):**
1. Assess size first:
   - **Trivial** (typo, config): `/gsd:fast` — inline, no overhead
   - **Small** (< 30 min, < 5 files): `/gsd:quick` — atomic commits, state tracking
2. Use `--discuss` flag if requirements need clarification
3. Use `--full` flag if the task turns out to be bigger than expected
4. `verification-before-completion`

**Steps (standalone):**
1. Assess scope — if truly small (< 3 steps), just do it
2. If it grows, invoke `brainstorming` → `writing-plans` → `subagent-driven-development`
3. `verification-before-completion`

**Escalation rule**: If a "quick" task exceeds 30 minutes or touches 5+ files, stop and upgrade to full phase planning (GSD) or plan-then-execute (SP).

---

## 5. Brownfield Onboarding

**When**: Existing codebase, first time using GSD/SP workflow on it.

**Steps:**
1. `/gsd:map-codebase` — parallel mapper agents analyze tech, architecture, quality, concerns
2. Review generated docs in `.planning/codebase/`
3. `/gsd:new-project` — define project goals, create ROADMAP.md using codebase analysis
4. Continue with Workflow 1 (greenfield) from step 2

**Alternative (lighter)**: `/gsd:scan` for a rapid assessment instead of full mapping.

**Key insight**: `map-codebase` produces documents that `new-project` consumes. Run them in this order.

---

## 6. Resume Across Sessions

**When**: Returning to work after a break or context reset.

**Steps (GSD project — `.planning/` exists):**
1. `/gsd:resume-work` — reads STATE.md, HANDOFF.md, restores full context
2. Follow the resumed state — typically picks up at execute or verify step
3. If mid-execution, the executor continues from checkpoint

**Steps (standalone — no `.planning/`):**
1. Read plan files in `docs/plans/` to restore context
2. Check git log for recent commits to understand progress
3. Pick up from the last incomplete step in the plan
4. Consider upgrading to GSD if the project has grown complex

**Shortcut**: Run `/torque:next` — it reads all state and tells you exactly what to do.

**Context restoration priority:**
1. `.planning/STATE.md` (most reliable, machine-written)
2. `.planning/phases/N/HANDOFF.md` (human-readable pause point)
3. `docs/plans/*.md` (SP plans, check completion marks)
4. `git log --oneline -20` (last resort, infer from commits)

---

## 7. End of Phase / Milestone

**When**: Phase work is complete, need to close out properly.

**Phase completion:**
1. `/gsd:verify-work` — validate features match phase acceptance criteria
2. `requesting-code-review` — code quality review
3. `finishing-a-development-branch` — merge/PR
4. `/gsd:progress` — check if more phases remain in milestone

**Milestone completion:**
1. Complete all phases (steps above)
2. `/gsd:audit-milestone` — verify milestone intent is fully delivered
3. If gaps found: `/gsd:plan-milestone-gaps` → execute gap phases
4. `/gsd:complete-milestone` — archive and prepare for next version
5. `/gsd:cleanup` — archive accumulated phase directories

**Post-milestone:**
- `/gsd:new-milestone` if continuing the project
- Update project README/docs to reflect delivered functionality

---

## 8. UI/Frontend Phase

**When**: A phase involves frontend, UI, or design work.

**Steps:**
1. `/gsd:ui-phase` — produce UI-SPEC.md design contract
   - Detects existing design system
   - Defines component hierarchy, responsive rules, accessibility
2. `/gsd:discuss-phase` → `/gsd:plan-phase` — normal planning, informed by UI-SPEC.md
3. `/gsd:execute-phase` — implement against the design contract
4. `/gsd:ui-review` — 6-pillar visual audit of implementation
5. `/gsd:verify-work` + `requesting-code-review` — standard completion

**Key insight**: UI-SPEC.md is the contract between design intent and implementation. Skip it and you get inconsistent UI.

---

## 9. Autonomous Execution

**When**: You want to run all remaining phases hands-off.

**Steps:**
1. Verify all prerequisite phases are complete
2. `/gsd:autonomous` — runs discuss → plan → execute for each remaining phase
3. Review results after completion
4. Run `requesting-code-review` on the full body of work

**When to use**: Well-defined roadmap with clear phase goals. Not recommended for exploratory or research-heavy work.

---

## 10. Security Audit

**When**: Phase involves security-sensitive code (auth, payments, data handling).

**Steps:**
1. Complete the phase normally (discuss → plan → execute → verify)
2. `/gsd:secure-phase` — verify threat mitigations exist in code
3. Review SECURITY.md output
4. Fix any gaps before code review
5. `requesting-code-review` with security focus

---

## Utility Commands

These don't follow a workflow — use them as needed:

| Command | When to Use |
|---------|-------------|
| `/torque:status` | See where you are across both systems |
| `/torque:check` | Verify GSD + SP installation health |
| `/torque:next` | Get one recommended next action |
| `/torque:migrate` | Assess upgrade path to Meridian |
| `/gsd:intel` | Query codebase intelligence before planning |
| `/gsd:stats` | View project metrics and timeline |
| `/gsd:note` | Capture an idea for later |
| `/gsd:plant-seed` | Save an idea with trigger conditions |
| `/gsd:session-report` | Summarize current session's work |
