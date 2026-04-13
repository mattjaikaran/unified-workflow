---
name: forge:check
description: "Verify GSD and Superpowers installation, detect version mismatches, and flag common misconfigurations. Run this when things aren't working as expected."
---

# Forge Health Check

Verify that GSD and Superpowers are installed correctly and the current project has no configuration conflicts.

## Procedure

### Step 1: Check GSD installation

```bash
# Check for GSD tools
ls ~/.claude/get-shit-done/bin/gsd-tools.cjs 2>/dev/null && echo "GSD_INSTALLED=true" || echo "GSD_INSTALLED=false"

# Get GSD version
cat ~/.claude/get-shit-done/VERSION 2>/dev/null || echo "GSD_VERSION=unknown"

# Check GSD skills are registered
ls ~/.claude/skills/gsd-* 2>/dev/null | head -5
```

### Step 2: Check Superpowers installation

```bash
# Check for core SP skills
for skill in systematic-debugging test-driven-development brainstorming writing-plans subagent-driven-development requesting-code-review verification-before-completion finishing-a-development-branch; do
  if ls ~/.claude/skills/$skill/SKILL.md 2>/dev/null > /dev/null; then
    echo "  ✓ $skill"
  else
    echo "  ✗ $skill (MISSING)"
  fi
done
```

### Step 3: Check Forge installation

```bash
# Verify this skill is properly installed
ls ~/.claude/skills/forge/SKILL.md 2>/dev/null && echo "FORGE_INSTALLED=true" || echo "FORGE_INSTALLED=false"
```

### Step 4: Project-level checks

Run these checks in the current working directory:

1. **Mixed plan locations**: Flag if both `.planning/` AND `docs/plans/` exist
2. **Orphaned .planning/**: Flag if `.planning/` exists but has no `PROJECT.md` (manually created)
3. **Stale state**: Flag if `.planning/STATE.md` references a phase that doesn't have a directory
4. **Missing reviews**: Flag if any phase has `VERIFICATION.md` but no code review was done (check git log for review commits)

### Step 5: Output format

```
## Forge Health Check

### Dependencies
| System | Status | Version |
|--------|--------|---------|
| GSD | ✓ Installed | 1.34.2 |
| Superpowers | ✓ Installed (8/8 core skills) | — |
| Forge | ✓ Installed | 0.2.0 |

### Project Checks
✓ Plan location is consistent (.planning/ only)
✓ STATE.md references valid phases
✗ Phase 2 was verified but never code-reviewed

### Recommendations
- Run `requesting-code-review` for Phase 2 before merging
```

If a dependency is missing:
```
### Dependencies
| System | Status | Version |
|--------|--------|---------|
| GSD | ✗ NOT FOUND | — |

### How to Install
GSD: https://github.com/cline/gsd
Superpowers: https://github.com/nicobailon/claude-code-superpowers
```

## Rules

- Never modify any files. This is read-only.
- Check all dependencies even if the first one fails.
- Be specific about what's wrong and how to fix it.
