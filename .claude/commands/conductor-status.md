---
description: Display current Conductor project progress
---

# Conductor Status

Show the current status of this Conductor project.

## 1. Check Setup

If `conductor/tracks.md` doesn't exist, tell user to run `/conductor-setup` first.

## 2. Read State

- Read `conductor/tracks.md`
- List all track directories: `conductor/tracks/*/`
- Read each `conductor/tracks/<track_id>/plan.md`

## 3. Calculate Progress

For each track:
- Read `metadata.json` to get priority and depends_on
- Count total tasks (lines with `- [ ]`, `- [~]`, `- [x]`)
- Count completed `[x]`
- Count in-progress `[~]`
- Count pending `[ ]`
- Calculate percentage: (completed / total) * 100
- Check if blocked (has incomplete dependencies)

## 4. Present Summary

Format the output like this:

```
## Conductor Status

**Active Track:** [track name] ([completed]/[total] tasks - [percent]%)
**Overall Status:** In Progress | Complete | No Active Tracks

### Priority Grouping

Group tracks by priority (read from metadata.json):
- 🔴 Critical
- 🟠 High  
- 🟡 Medium
- 🟢 Low

### Tracks by Priority

**🔴 Critical**
- [~] auth_20241215 - User Authentication (30%)

**🟠 High**
- [ ] payments_20241216 - Payment Integration 🔒
  ⤷ Depends on: auth_20241215

**🟡 Medium**
- [x] setup_20241214 - Project Setup (100%)

**🟢 Low**
- [ ] docs_20241217 - Documentation

### Blocked Tracks

Show 🔒 for tracks with incomplete dependencies.
Show dependency chain: "Depends on: [track_ids]"

### Blockers
List all tasks marked with `[!]`:
- Task name (track_id): Reason

### Current Task
[The task marked with [~] in the active track's plan.md]

### Next Action
[The next task marked with [ ] in the active track's plan.md]

### Recent Completions
[Last 3 tasks marked [x] with their commit SHAs]
```

## 5. Suggestions

Based on status:
- If no tracks: "Run `/conductor-newtrack` to create your first track"
- If track in progress: "Run `/conductor-implement` to continue"
- If all complete: "All tracks complete! Run `/conductor-newtrack` for new work"
