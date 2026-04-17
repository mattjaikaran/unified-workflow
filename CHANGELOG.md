# Changelog

All notable changes to Torque will be documented in this file.

## [0.3.0] - 2026-04-16

### Changed
- **Renamed to Torque** from Forge — name collision avoidance
- All commands updated: `/torque:status`, `/torque:check`, `/torque:next`, `/torque:migrate`
- Updated all references, install paths, and documentation

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
