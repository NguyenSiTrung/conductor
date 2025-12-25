---
description: Create context handoff for transferring implementation to next section/session
---

# Conductor Handoff

Create a comprehensive context handoff document when you need to transfer implementation progress to a new section or session. Essential for large tracks that span multiple AI context windows.

## 1. Identify Active Track
- Find track marked `[~]` in `conductor/tracks.md`
- If no active track, ask user to specify or halt
- Load `spec.md`, `plan.md`, and `implement_state.json`

## 2. Gather Handoff Context

**Progress Analysis:**
- Count completed `[x]`, in-progress `[~]`, pending `[ ]` tasks
- Calculate overall percentage
- Identify current phase and task position

**Recent Changes:**
```bash
git log --oneline -10
git diff --name-only HEAD~5
```

**Unresolved Issues:**
- Check for `[!]` blocked markers in plan.md
- Read `blockers.md` if exists
- Ask user for any pending decisions or important context

## 3. Update Implementation State

Update `conductor/tracks/<track_id>/implement_state.json` with section tracking:
```json
{
  "current_phase": "...",
  "current_phase_index": 1,
  "current_task_index": 3,
  "completed_phases": ["Phase 1"],
  "section_count": 2,
  "last_handoff": "<ISO timestamp>",
  "handoff_history": [
    {
      "section": 1,
      "timestamp": "...",
      "phase_at_handoff": "...",
      "task_at_handoff": 5,
      "handoff_file": "handoff_<timestamp>.md"
    }
  ],
  "status": "handed_off"
}
```

## 4. Create Handoff Document

Create `conductor/tracks/<track_id>/handoff_<YYYYMMDD_HHMMSS>.md` with:

- **Header:** Track info, section number, timestamp, link to previous handoff
- **Progress Summary:** Overall %, current phase/task, completed/remaining tasks
- **Key Implementation Decisions:** Important choices made during this section
- **Code Changes Summary:** Files modified, new files, recent commits
- **Unresolved Issues:** Blockers, pending decisions, questions
- **Context for Next Section:** Critical info, architecture notes, testing status
- **Next Steps:** Immediate tasks, upcoming phase work
- **Resume Instructions:** Commands and specific actions to continue

## 5. Commit Handoff

```bash
git add conductor/tracks/<track_id>/
git commit -m "conductor(handoff): Create section <N> handoff for <track_id>

Progress: <X>% complete
Phase: <current_phase>
Next: <next_task_brief>"
```

## 6. Present Summary

Display:
- Handoff document location
- Resume command (`/conductor-implement <track_id>`)
- Next action to take
- Options: End session, Continue, View full document

## When to Use

- Before ending a long implementation session
- When context window is getting full
- At phase boundaries with significant remaining work
- When transferring work to a different session/agent
- After 5+ tasks completed without a checkpoint
