# Changelog

All notable changes to Torque will be documented in this file.

## [0.3.0] - 2026-04-18

### Added
- **Spec phase routing**: `/gsd:spec-phase` between discuss and plan for ambiguous requirements — produces SPEC.md with falsifiable requirements
- **AI/LLM integration path**: Full routing for `/gsd:ai-integration-phase` → plan → execute → `/gsd:eval-review` with AI-SPEC.md design contract
- **Spike/sketch exploration**: Pre-planning workflows via `/gsd:spike` and `/gsd:sketch` for feasibility testing and UI mockups
- **PRD pipeline**: `write-a-prd` → `prd-to-issues` → route to GSD/SP execution
- **Ship flow**: `/gsd:ship` and `/gsd:pr-branch` routing — clean PR creation that filters `.planning/` commits
- **Receiving code review**: `receiving-code-review` routing — verify review feedback before implementing
- **Plan stress-testing**: `grill-me` routing — interview loop to resolve ambiguity before committing
- **Architecture analysis**: `improve-codebase-architecture` routing — surface friction, propose RFC issues
- **Backlog/todo management**: Full routing for `/gsd:add-backlog`, `/gsd:review-backlog`, `/gsd:add-todo`, `/gsd:check-todos`, `/gsd:explore`
- **Browser testing**: `webapp-testing` and `playwright-skill` routing
- **Changelog generation**: `changelog-generator` routing
- **Extended utilities**: Routing for `/gsd:undo`, `/gsd:health`, `/gsd:forensics`, `/gsd:validate-phase`, `/gsd:extract_learnings`, `/gsd:docs-update`
- 5 new integration rules (7→12): AI discipline, spec refinement, spike-before-commit, receiving review, ship cleanly
- 5 new workflow guides (10→15): AI/LLM integration, spike/exploration, PRD-driven dev, architecture improvement, receiving review
- 5 new anti-patterns documented
- 3 new conflict resolution scenarios (7→10): blind review implementation, `.planning/` in PRs, AI without eval
- Extended check skill: verifies 9 core + 9 optional SP skills
- Extended migrate skill: maps 24 commands to Meridian equivalents (was 12)
- Extended next skill: detects spec-phase gaps, AI phases, and ship readiness

### Changed
- **Renamed to Torque** from Forge — name collision avoidance
- All commands updated: `/torque:status`, `/torque:check`, `/torque:next`, `/torque:migrate`
- Updated all references, install paths, and documentation
- Primary routing table expanded from 10 to 17 scenarios
- Extended routing table expanded from 11 to 25 commands
- Decision flowchart expanded with spike, AI, PRD, architecture, and idea capture branches
- Complexity escalation updated: added AI/LLM and uncertain-feasibility paths
- Comparison matrix updated with new Torque-unique features

## [0.2.0] - 2026-04-13

### Added
- **Unified workflow skill** — GSD + Superpowers, united into one workflow
- **`/torque:status`** — unified project state view across GSD + Superpowers
- **`/torque:check`** — dependency health check and misconfiguration detection
- **`/torque:next`** — smart next-action recommendation based on project state
- **`/torque:migrate`** — upgrade path assessment and guide to Meridian
- Extended routing table covering GSD 1.34+ features:
  - UI phases (`/gsd:ui-phase`, `/gsd:ui-review`)
  - Security audits (`/gsd:secure-phase`)
  - Codebase intelligence (`/gsd:intel`)
  - Autonomous execution (`/gsd:autonomous`)
  - Parallel workstreams (`/gsd:workstreams`)
  - Persistent debugging (`/gsd:debug`)
  - Code review + auto-fix (`/gsd:code-review`, `/gsd:code-review-fix`)
  - Session reports and project stats
- Complexity escalation guide (trivial → small → medium → large → autonomous)
- Conflict resolution reference guide
- Comparison matrix: Torque vs Meridian vs raw GSD+SP
- Version tracking in SKILL.md frontmatter

### Changed
- Expanded routing table from 10 to 20+ scenarios
- Added integration rules for UI discipline and learnings persistence
- Updated all reference docs for GSD 1.34 compatibility
- Overhauled README with positioning, comparison, and upgrade path

## [0.1.0] - 2026-03-07

### Added
- Initial routing skill (as "unified-workflow") with `.planning/` detection
- Primary routing table (10 scenarios)
- Two-layer model documentation (GSD macro + SP micro)
- Integration rules (5 rules)
- Anti-pattern documentation
- Reference guides:
  - Decision flowchart
  - Integration rules for GSD executors + SP discipline
  - 7 step-by-step workflow guides
