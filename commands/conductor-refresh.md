---
description: Sync context docs with current codebase state
---

# Conductor Refresh

Refresh conductor context documentation to match the current state of the codebase. Use when documentation has become stale due to codebase evolution, new dependencies, or shipped features.

## 1. Verify Setup

Check these files exist:
- `conductor/product.md`
- `conductor/tech-stack.md`
- `conductor/workflow.md`
- `conductor/tracks.md`

If missing, tell user to run `/conductor-setup` first.

## 2. Determine Scope

If scope argument provided, use it. Otherwise, ask:

```
What would you like to refresh?
1. all - Full refresh of all context documents
2. tech - Update tech-stack.md (dependencies, frameworks, tools)
3. product - Update product.md (shipped features, evolved goals)
4. workflow - Update workflow.md (process changes)
5. track [id] - Refresh specific track's spec/plan
```

## 3. Analyze Current State

### For `tech` scope:
1. Read current `conductor/tech-stack.md`
2. Scan codebase for dependency files:
   - `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pom.xml`
3. Identify:
   - New packages/frameworks detected
   - Removed dependencies still documented
   - Major version changes

### For `product` scope:
1. Read current `conductor/product.md`
2. Analyze:
   - Completed tracks in `conductor/tracks.md` (shipped features)
   - README.md changes
   - New major components or features
3. Identify features shipped but not in product.md

### For `workflow` scope:
1. Read current `conductor/workflow.md`
2. Check for:
   - New CI/CD configurations (`.github/workflows/`, etc.)
   - New linting/testing tools
   - Changed commit conventions

### For `track` scope:
1. Load specified track's spec.md and plan.md
2. Compare against actual implementation
3. Identify completed items not marked, or spec drift

### For `all` scope:
- Run all of the above analyses

## 4. Generate Drift Report

Present findings:

```
## Context Refresh Analysis

**Last setup:** [date from setup_state.json]
**Days since setup:** [N days]

### Tech Stack Drift
- **Added:** [new packages/frameworks detected]
- **Removed:** [packages in docs but not in codebase]
- **Version changes:** [major version updates]

### Product Drift
- **Shipped features:** [completed tracks not in product.md]
- **New components:** [directories/modules not documented]

### Workflow Drift
- **New tools:** [CI/CD, linting, testing additions]
- **Process changes:** [detected convention changes]

### Recommended Updates
1. [Specific update 1]
2. [Specific update 2]
```

## 5. Confirm Updates

Ask user:
```
Apply these updates?
1. All recommended updates
2. Select specific updates
3. Cancel
```

## 6. Apply Updates

For each confirmed update:

1. Create backup: `cp conductor/<file>.md conductor/<file>.md.bak`
2. Apply changes to relevant files
3. Add refresh marker at top:
   ```markdown
   > **Last Refreshed:** [Date] - Context synced with codebase
   ```

## 7. Update Refresh State

Create/update `conductor/refresh_state.json`:
```json
{
  "last_refresh": "ISO timestamp",
  "scope": "all|tech|product|workflow|track",
  "changes_applied": [
    {"file": "tech-stack.md", "changes": ["added X", "removed Y"]}
  ],
  "next_refresh_hint": "ISO timestamp (2 days from now)"
}
```

## 8. Commit Changes

```bash
git add conductor/
git commit -m "conductor(refresh): Sync context with codebase

Scope: [scope]
- [key changes summary]"
```

## 9. Announce

```
Context refresh complete:
- tech-stack.md: [updated/unchanged]
- product.md: [updated/unchanged]
- workflow.md: [updated/unchanged]

Next suggested refresh: [date 2 days from now]
```
