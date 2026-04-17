# Comparison: Torque vs Meridian vs Raw GSD+SP

## Overview

| | Raw GSD + SP | Torque | Meridian |
|--|-------------|-----------------|----------|
| **What it is** | Two separate skill systems | Routing layer that coordinates them | Standalone workflow engine |
| **Setup** | Install both, figure out when to use which | Install both + this router | Install one system |
| **State storage** | `.planning/` files (GSD) | `.planning/` files (GSD) | SQLite database |
| **Dependencies** | GSD + Superpowers | GSD + Superpowers | None (stdlib only) |
| **Commands** | 60+ GSD + 104 SP skills | 4 Torque + routes to GSD/SP | 39 native commands |
| **Lines of code** | ~15k combined | ~1.5k | ~11k |
| **Resume** | File-based (STATE.md, HANDOFF.md) | File-based (same as GSD) | Deterministic (SQLite → prompt) |
| **Code review** | Manual (SP skill) | Guided (integration rules) | Built-in two-stage |
| **Debugging** | SP systematic-debugging | Same, with routing guidance | 4-phase with persistent KB |
| **Quality gates** | Per-system | Suggested via integration rules | Enforced (regression, stubs, coverage) |
| **Board sync** | No | No | Yes (Linear, Jira, custom) |
| **Remote dispatch** | No | No | Yes (HTTP agents) |
| **Execution learnings** | GSD learnings (1.34+) | Same | Auto-capture + rule injection |

## Feature Matrix

### Project Management
| Feature | Torque | Meridian |
|---------|-----------------|----------|
| Project initialization | ✓ (routes to GSD) | ✓ (native) |
| Milestone tracking | ✓ (routes to GSD) | ✓ (native) |
| Phase planning | ✓ (routes to GSD) | ✓ (native) |
| Plan execution | ✓ (routes to GSD) | ✓ (native, wave-parallel) |
| Quick tasks | ✓ (routes to GSD) | ✓ (native) |
| Autonomous mode | ✓ (routes to GSD) | ✓ (native) |

### Engineering Discipline
| Feature | Torque | Meridian |
|---------|-----------------|----------|
| TDD enforcement | ✓ (routes to SP) | ✓ (native, per-plan flag) |
| Systematic debugging | ✓ (routes to SP) | ✓ (native + knowledge base) |
| Code review | ✓ (routes to SP) | ✓ (native two-stage) |
| Verification | ✓ (routes to SP) | ✓ (native + Nyquist check) |
| Branch completion | ✓ (routes to SP) | ✓ (native ship command) |

### Unique to Torque
| Feature | Description |
|---------|-------------|
| Zero config | No database, no initialization. Just install and go. |
| System agnostic | Leverages GSD AND SP — uses the best of both |
| Lightweight | ~1.5k lines total. Installs in seconds. |
| Health check | `/torque:check` verifies both systems work |
| Smart routing | One question (`.planning/`?) routes everything |

### Unique to Meridian
| Feature | Description |
|---------|-------------|
| Deterministic resume | Same SQLite state = same prompt, guaranteed |
| Board sync | Pluggable Linear/Jira integration |
| Remote dispatch | Send plans to other agents/machines |
| Debug knowledge base | Resolved bugs persist across sessions |
| Execution learnings | System improves from failures automatically |
| Decision tracing | Unique IDs (DEC-001) with audit trails |
| Session intelligence | Token tracking, developer profiling |
| Context window discipline | Checkpoint at estimated token limits |

## When to Use Which

### Use Torque when:
- You already have GSD and Superpowers and like them
- Your project is straightforward (1-3 milestones)
- You want zero setup overhead
- You prefer the flexibility of two specialized systems
- Lightweight coordination is all you need

### Use Meridian when:
- You want one system instead of two
- Session continuity is critical (long-running projects)
- You need deterministic resume across context resets
- You want enforced quality gates (not just suggested)
- You need board sync, remote dispatch, or execution learnings
- Your project spans 3+ milestones or has complex dependencies

### Use raw GSD + SP when:
- You understand both systems well and don't need routing guidance
- You have your own coordination workflow
- You only use one system (GSD for management OR SP for discipline, not both)

## Migration Triggers

Consider upgrading from Torque to Meridian when:

1. **3+ HANDOFF.md files** in your project — session continuity is a recurring pain
2. **2+ milestones completed** — project complexity justifies the investment
3. **Repeated conflict resolution** — you keep hitting the issues in `conflict-resolution.md`
4. **Team growth** — multiple people or agents need coordinated access
5. **Quality gaps** — verification and review are being skipped despite integration rules

Run `/torque:migrate` for a project-specific assessment.
