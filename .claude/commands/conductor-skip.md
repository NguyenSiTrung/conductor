---
description: Skip the current task with a reason
---

# Conductor Skip

Skip the current in-progress task.

## 1. Find Current Task
- Find in-progress track in `tracks.md`
- Find in-progress task in that track's `plan.md`

## 2. Get Reason
Ask why skipping:
- Will complete later
- No longer needed
- Blocked by external factor
- Other

## 3. Update Plan
- If "no longer needed": Mark as `[x] (SKIPPED)`
- Otherwise: Reset to `[ ]` with skip comment
- Mark next pending task as `[~]`

## 4. Update State
Update `implement_state.json` with new task index.

## 5. Announce
Confirm skip and show next task.
