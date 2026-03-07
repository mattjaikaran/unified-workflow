# Unified Workflow Guides

Seven step-by-step guides covering the most common development scenarios.

---

## 1. New Project (Greenfield)

**When**: Starting a project from scratch with no existing code.

**Steps:**
1. `/gsd:new-project` — deep context gathering, creates PROJECT.md and ROADMAP.md
2. `/gsd:discuss-phase` for Phase 1 — gather requirements, produce CONTEXT.md
3. `/gsd:plan-phase` for Phase 1 — create executable PLAN.md files
4. `/gsd:execute-phase` for Phase 1 — fresh-context executors build each plan
5. `/gsd:verify-work` — validate features match phase goals
6. Invoke **REQUIRED SUB-SKILL:** `requesting-code-review` — review code quality
7. Invoke **REQUIRED SUB-SKILL:** `finishing-a-development-branch` — merge/PR decision
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
7. Invoke **REQUIRED SUB-SKILL:** `requesting-code-review` — code quality check
8. Invoke **REQUIRED SUB-SKILL:** `finishing-a-development-branch` — merge/PR

**Do NOT**: Run `brainstorming` or `writing-plans` (SP standalone skills). Use `discuss-phase` instead — it feeds CONTEXT.md to downstream GSD agents.

---

## 3. Bug Fix

**When**: A bug is reported or discovered, regardless of project type.

**Steps:**
1. Invoke **REQUIRED SUB-SKILL:** `systematic-debugging` — ALWAYS first
   - Phase 1: Reproduce and characterize
   - Phase 2: Form hypotheses, rank by likelihood
   - Phase 3: Binary search / targeted investigation
   - Phase 4: Root cause fix and regression test
2. Route the fix based on project context:
   - **GSD project**: `/gsd:quick` for small fixes, or update current phase plan
   - **Standalone**: Apply fix directly, commit
3. Invoke **REQUIRED SUB-SKILL:** `verification-before-completion` — prove the fix works

**Do NOT**: Skip systematic-debugging even in GSD projects. GSD deviation Rule 1 ("auto-fix bugs") only covers incidental executor bugs, not user-reported issues.

---

## 4. Quick / Ad-Hoc Task

**When**: Small task that doesn't warrant full phase planning.

**Steps (GSD project):**
1. `/gsd:quick` — executes with atomic commits and state tracking
2. Use `--discuss` flag if requirements need clarification
3. Use `--full` flag if the task turns out to be bigger than expected
4. Invoke **REQUIRED SUB-SKILL:** `verification-before-completion`

**Steps (standalone):**
1. Assess scope — if truly small (< 3 steps), just do it
2. If it grows, invoke `brainstorming` → `writing-plans` → `subagent-driven-development`
3. Invoke **REQUIRED SUB-SKILL:** `verification-before-completion`

**Escalation rule**: If a "quick" task exceeds 30 minutes or touches 5+ files, stop and upgrade to full phase planning (GSD) or plan-then-execute (SP).

---

## 5. Brownfield Onboarding

**When**: Existing codebase, first time using GSD/SP workflow on it.

**Steps:**
1. `/gsd:map-codebase` — parallel mapper agents analyze tech, architecture, quality, concerns
2. Review generated docs in `.planning/codebase/`
3. `/gsd:new-project` — define project goals, create ROADMAP.md using codebase analysis
4. Continue with Workflow 1 (greenfield) from step 2

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
2. Invoke **REQUIRED SUB-SKILL:** `requesting-code-review` — code quality review
3. Invoke **REQUIRED SUB-SKILL:** `finishing-a-development-branch` — merge/PR
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
