# Integration Rules: GSD Executors + Superpowers Discipline

## Rule 1: TDD in GSD Executors

GSD plans with `tdd="true"` already follow RED-GREEN-REFACTOR, matching SP's TDD iron law. No special handling needed — the disciplines align naturally.

When a GSD plan does NOT specify TDD but the task involves new logic:
- The executor should still write tests first per SP `test-driven-development`
- Add test files to the plan's file list in SUMMARY.md

## Rule 2: Verification Enhancement

GSD executors run self-check per plan. Enhancement for Torque:
1. GSD executor completes plan → writes SUMMARY.md
2. Before marking plan complete, run full project test suite (not just plan-scoped tests)
3. If tests fail, treat as deviation Rule 1 (auto-fix if < 5 min)
4. If fix exceeds 5 min, escalate — do not mark plan complete

## Rule 3: Compressed Debugging in Executors

When a GSD executor hits an unexpected bug (deviation Rule 1 scope):

Apply compressed SP debugging (NOT full 4-phase):
1. **Read error** — capture exact error message and stack trace
2. **Trace to source** — follow the stack to the root cause file/line
3. **Fix at source** — address root cause, not symptoms
4. **Document** — note root cause in plan's SUMMARY.md deviation section

This prevents symptom-fixing while staying within executor time bounds.

Full SP `systematic-debugging` (4-phase with hypotheses and binary search) is reserved for user-reported bugs and complex failures that exceed executor scope.

## Rule 4: Code Review After GSD Verification

GSD's verify-work validates that built features match phase goals. It does NOT review code quality, patterns, or maintainability. Fill this gap:

**After GSD phase completion:**
1. `/gsd:execute-phase` — build the work
2. `/gsd:verify-work` — validate features match goals
3. **REQUIRED:** `requesting-code-review` — review code quality
4. **REQUIRED:** `finishing-a-development-branch` — merge/PR decision

Steps 3-4 are SP skills that GSD doesn't provide. Always run them before considering a phase truly complete.

## Rule 5: Branch Completion Protocol

Use SP `finishing-a-development-branch` as the final gate after both:
- GSD verification passes (features work)
- SP code review passes (code quality acceptable)

This skill guides the merge/PR/cleanup decision.

## Rule 6: UI Discipline (GSD 1.34+)

For phases involving frontend/UI work:

1. Run `/gsd:ui-phase` BEFORE implementation — produces UI-SPEC.md design contract
2. Execute the phase normally (`/gsd:execute-phase`)
3. Run `/gsd:ui-review` AFTER implementation — 6-pillar visual audit

UI-SPEC.md serves as the contract between design intent and implementation. The review validates adherence.

## Rule 7: AI/LLM Discipline (GSD 1.34+)

For phases involving AI features (LLM calls, embeddings, RAG, agents):

1. Run `/gsd:ai-integration-phase` BEFORE implementation — produces AI-SPEC.md:
   - Framework selection via interactive decision matrix
   - Implementation guidance from official docs (best practices, pitfalls)
   - Evaluation strategy with rubrics, failure modes, and guardrails
2. Execute the phase normally (`/gsd:execute-phase`)
3. Run `/gsd:eval-review` AFTER implementation — scores each eval dimension as COVERED/PARTIAL/MISSING

AI-SPEC.md is the contract for AI phases, just as UI-SPEC.md is for frontend phases. Without it, you build without eval criteria and only discover failure modes in production.

## Rule 8: Spec Refinement Before Planning

For medium+ features with ambiguous requirements:

1. Run `/gsd:discuss-phase` to gather context (produces CONTEXT.md)
2. Run `/gsd:spec-phase` to lock falsifiable requirements (produces SPEC.md)
3. Then run `/gsd:plan-phase` informed by both CONTEXT.md and SPEC.md

Skip spec-phase only for well-understood work (clear scope, no ambiguity). When in doubt, spec it — vague requirements produce vague implementations.

## Rule 9: Spike Before Committing

When feasibility is uncertain:

1. Run `/gsd:spike` — time-boxed throwaway experiment to validate ONE assumption
2. Run `/gsd:spike-wrap-up` — package findings into a persistent skill
3. Use findings to inform `/gsd:discuss-phase`

For UI exploration, use `/gsd:sketch` → `/gsd:sketch-wrap-up` instead.

**Rule**: Never plan a full phase around an unvalidated technical assumption. Spike first, plan second.

## Rule 10: Receiving Code Review

When receiving review feedback on a PR:

1. Run `receiving-code-review` (SP) to verify each suggestion
2. Check: Is the concern valid? Does the fix work? Would it cause regressions?
3. Implement verified fixes, push back on incorrect suggestions with evidence

**Rule**: Never implement review feedback blindly. Reviewers lack full context. Verify before acting.

## Rule 11: Ship Cleanly

When creating PRs from GSD projects:

1. Use `/gsd:ship` — creates PR, runs review, prepares for merge
2. Or use `/gsd:pr-branch` — creates a branch that filters out `.planning/` commits

**Rule**: Never push `.planning/` directory changes in a code review PR. Use `/gsd:pr-branch` to keep PRs focused on code.

## Rule 12: Learnings Integration (GSD 1.34+)

GSD 1.34+ captures execution patterns automatically via the learnings system.

Use `/gsd:extract_learnings` after completing a phase to extract decisions, lessons, patterns, and surprises. These feed into `/gsd:intel` for future planning.

- After failures: patterns are recorded to prevent recurrence
- Before planning: `/gsd:intel` surfaces relevant learnings
- Cross-phase: learnings from early phases inform later planning

**Torque enhancement**: After SP code review finds recurring issues, capture the pattern:
- GSD projects: learnings are auto-captured by the system
- SP standalone: manually note patterns in `docs/lessons.md`

---

## Plan Location Convention

| System | Plan Directory | State Files | Design Docs |
|--------|---------------|-------------|-------------|
| GSD | `.planning/phases/N/` | `.planning/STATE.md` | `.planning/phases/N/CONTEXT.md` |
| SP standalone | `docs/plans/` | None (stateless) | `docs/plans/*.md` |

**Rule**: Never create `docs/plans/` in a GSD project. Never create `.planning/` for standalone SP work.

## State Management

| Concern | GSD Approach | SP Approach | Unified Rule |
|---------|-------------|-------------|--------------|
| Session persistence | STATE.md + HANDOFF.md | None | Use GSD if `.planning/` exists |
| Progress tracking | Phase completion % | Plan file checkboxes | Use whichever system owns the work |
| Context restoration | `/gsd:resume-work` | Re-read plan files | Check `.planning/` first |
| Pause/resume | `/gsd:pause-work` creates handoff | Manual | Always prefer GSD pause if available |

## Context Rot Prevention

GSD's key insight: fresh subagents with 200k context per plan prevent context rot in long projects.

**Rules:**
- For GSD projects, trust GSD's executor model — don't add SP subagent layers on top
- For standalone SP work, use `subagent-driven-development` which has its own 2-stage review
- Never nest subagent models (GSD executor spawning SP subagent-driven-dev)
- The coordinator (main conversation) stays lean: route, review, don't accumulate implementation details

## GSD Code Review Integration (1.34+)

GSD 1.34 added native code review commands that complement SP review:

| Step | Tool | What It Checks |
|------|------|----------------|
| 1. Feature validation | `/gsd:verify-work` | Does it match phase goals? |
| 2. Nyquist validation | `/gsd:validate-phase` | Validation gap audit (optional) |
| 3. Code quality (GSD) | `/gsd:code-review` | Bugs, security, code smells |
| 4. Auto-fix (GSD) | `/gsd:code-review-fix` | Atomic fixes for review findings |
| 5. Code quality (SP) | `requesting-code-review` | Patterns, maintainability, style |
| 6. Ship | `/gsd:ship` or `/gsd:pr-branch` | Clean PR creation (filters `.planning/`) |

Steps 2-4 are optional but recommended. Step 5 remains required — SP review catches different issues than GSD's automated review. Step 6 replaces `finishing-a-development-branch` for GSD projects — it creates PRs without `.planning/` noise.
