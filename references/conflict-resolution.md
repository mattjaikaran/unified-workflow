# Conflict Resolution Guide

What to do when things go wrong in Forge.

---

## 1. Mixed Plan Locations

**Symptom**: Both `.planning/` AND `docs/plans/` exist in the same project.

**Why it's bad**: GSD executors read from `.planning/phases/`. SP subagents read from `docs/plans/`. Neither system sees the other's plans. You get duplicated work, missed steps, and conflicting state.

**Fix**:
1. Determine which system owns the project:
   - If `.planning/PROJECT.md` exists and has real content → GSD owns it
   - If `docs/plans/` has the more recent work → SP owns it
2. Migrate plans to the owning system:
   - **GSD wins**: Move relevant `docs/plans/` content into a new GSD phase via `/gsd:add-phase`, then delete `docs/plans/`
   - **SP wins**: This is rare. Usually means `.planning/` was created accidentally. Archive it: `mv .planning/ .planning.bak/`
3. Run `/forge:check` to verify the conflict is resolved

---

## 2. Stalled Phase

**Symptom**: A phase has been in "executing" for a long time with no progress. Plans are partially complete.

**Why it happens**: Context was lost between sessions, executor hit an unrecoverable error, or scope grew beyond what was planned.

**Fix**:
1. Run `/forge:status` to see exactly where things stalled
2. Check for `HANDOFF.md` in the phase directory — if present, run `/gsd:resume-work`
3. If no handoff exists:
   - Read the incomplete plan's SUMMARY.md to understand what was done
   - Check git log for the last related commit
   - Either resume execution or re-plan the remaining work
4. If the phase scope grew: split it with `/gsd:insert-phase` to break remaining work into a new phase

---

## 3. Context Rot

**Symptom**: The main conversation has accumulated too much implementation detail. Responses are slow, confused, or contradictory.

**Why it happens**: Long-running phases without fresh subagent boundaries. The coordinator (main conversation) is doing executor work instead of delegating.

**Fix**:
1. **Immediate**: Run `/gsd:pause-work` to create a HANDOFF.md snapshot
2. **Start fresh**: Open a new conversation, run `/gsd:resume-work`
3. **Prevent recurrence**: 
   - Trust GSD's executor model — let subagents do implementation
   - The coordinator should only route, review, and track state
   - For standalone SP work, use `subagent-driven-development` (not inline coding)

---

## 4. Verification Without Review

**Symptom**: Phase was verified (`/gsd:verify-work`) but never code-reviewed. Features work but code quality is unknown.

**Why it happens**: GSD's pipeline doesn't include code review. It's easy to forget the SP step.

**Fix**:
1. Run `requesting-code-review` now — it's never too late
2. If issues are found, fix them before shipping
3. The `/forge:next` command catches this gap and will suggest review when it's missing

---

## 5. Wrong System for the Task

**Symptom**: You started with `brainstorming` + `writing-plans` in a GSD project, or ran `/gsd:discuss-phase` for standalone work.

**Fix (used SP in a GSD project)**:
1. Don't delete the SP plan — it has useful thinking
2. Create a GSD phase: `/gsd:add-phase`
3. Run `/gsd:discuss-phase` — reference the SP plan's insights in the discussion
4. Delete `docs/plans/` when the GSD phase has absorbed the content

**Fix (used GSD for standalone work)**:
1. If `.planning/` was created with `/gsd:new-project` and has real content — just keep using GSD. It's fine for single-feature work too.
2. If `.planning/` was created accidentally — archive it and switch to SP

---

## 6. Subagent Nesting

**Symptom**: GSD `execute-phase` spawned an executor that internally ran SP `subagent-driven-development`. Double subagent layer causing context rot and competing state.

**Why it's bad**: Each layer burns ~200k tokens of context. Two layers means 400k tokens for one plan, with no state coordination between them.

**Fix**:
1. Stop the nested execution
2. GSD executors ARE the subagent layer — they already get fresh context per plan
3. SP engineering discipline (TDD, debugging) should be applied WITHIN the GSD executor, not as a nested subagent call
4. The integration rules in `integration-rules.md` specify how SP discipline integrates into GSD executors

---

## 7. When to Upgrade to Meridian

If you're hitting these conflicts repeatedly, consider whether Forge's routing model is adding overhead. Run `/forge:migrate` to assess.

**Signs the routing model is the problem**:
- You spend more time managing GSD + SP coordination than doing actual work
- Session continuity is painful despite HANDOFF.md
- You need features that span both systems (e.g., review history, execution learnings)
- Multi-milestone projects with complex phase dependencies

**Meridian replaces Forge's routing model entirely** — it's a single system that handles everything GSD and SP do, with SQLite-backed state and deterministic resume.
