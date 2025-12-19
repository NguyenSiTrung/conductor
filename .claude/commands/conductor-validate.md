---
description: Validate Conductor project integrity
---

# Conductor Validate

Validate the integrity of this Conductor project.

## 1. Core Files Check

Verify these files exist in `conductor/`:
- `product.md`
- `tech-stack.md`
- `workflow.md`
- `tracks.md`

## 2. Tracks Consistency

For each track in `tracks.md`:
- Verify directory exists: `conductor/tracks/<track_id>/`
- Verify files: `metadata.json`, `spec.md`, `plan.md`
- Validate metadata.json has: track_id, type, status, created_at

## 3. Orphan Detection

- List all directories in `conductor/tracks/`
- Report any not referenced in `tracks.md`

## 4. Status Consistency

- Compare status markers in `tracks.md` with `metadata.json` status field
- Report mismatches

## 5. Plan Integrity

For each `plan.md`:
- Must have at least one phase and task
- Valid markers only: `[ ]`, `[~]`, `[x]`, `[!]`
- Completed tracks should have all tasks completed

## 6. Report

Present summary with:
- ✅ Valid items
- ⚠️ Warnings
- ❌ Errors
- Recommendations for fixes

## 7. Auto-Fix Option

Offer to fix auto-fixable issues:
- Missing metadata fields
- Status mismatches
- Orphan cleanup
