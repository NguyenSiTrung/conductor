---
description: Execute tasks from a track's implementation plan
argument-hint: [track_id]
---

<!-- 
SYSTEM DIRECTIVE: You are an AI agent for the Conductor framework.
CRITICAL: Validate every tool call. If any fails, halt and announce the failure.
-->

# Conductor Implement

Implement track: $ARGUMENTS

---

## 1.0 SETUP CHECK

**PROTOCOL: Verify Conductor environment is properly set up.**

1. **Check Required Files:** Verify existence of:
   - `conductor/product.md`
   - `conductor/tech-stack.md`
   - `conductor/workflow.md`

2. **Handle Missing Files:**
   - If ANY missing: HALT immediately
   - Announce: "Conductor is not set up. Please run `/conductor-setup` first."
   - Do NOT proceed.

---

## 2.0 TRACK SELECTION

**PROTOCOL: Identify and select the track to be implemented.**

1. **Check for User Input:** Check if track name provided as argument.

2. **Parse Tracks File:** Read `conductor/tracks.md`
   - Split by `---` separator to identify track sections
   - Extract: status (`[ ]`, `[~]`, `[x]`), description, folder link
   - **CRITICAL:** If no track sections found: "The tracks file is empty or malformed." → HALT

3. **Select Track:**

   **If track name provided:**
   - Exact, case-insensitive match against descriptions
   - If unique match: Confirm "I found track '<description>'. Is this correct?"
   - If no match or ambiguous: Inform user, suggest next available track

   **If no track name provided:**
   - Find first track NOT marked `[x]`
   - Announce: "No track name provided. Selecting next incomplete track: '<description>'"
   - If all complete: "No incomplete tracks found. All tasks completed!" → HALT

4. **Check Dependencies:**
   - Read `conductor/tracks/<track_id>/metadata.json`
   - If `depends_on` array is not empty:
     - For each dependency, check status in `conductor/tracks.md`
     - If ANY not `[x]` (completed):
       > "⚠️ This track has incomplete dependencies:"
       > [List blocking tracks]
       > "Do you want to proceed anyway?"
       > A) Yes - Proceed despite incomplete dependencies
       > B) No - Implement dependencies first
     - If B: Suggest `/conductor-implement <first_dependency>`

5. **Handle No Selection:** If no track selected, inform user and await instructions.

---

## 3.0 TRACK IMPLEMENTATION

**PROTOCOL: Execute the selected track.**

1. **Announce Action:** State which track you're beginning to implement.

2. **Update Status to 'In Progress':**
   - In `conductor/tracks.md`, change `## [ ] Track:` to `## [~] Track:` for selected track

3. **Load Track Context:**
   - Identify track folder from tracks file link → get `<track_id>`
   - Read (using absolute paths):
     - `conductor/tracks/<track_id>/plan.md`
     - `conductor/tracks/<track_id>/spec.md`
     - `conductor/workflow.md`
   - **Error Handling:** If any read fails, STOP and inform user

4. **Check and Load Resume State:**

   **Check for:** `conductor/tracks/<track_id>/implement_state.json`

   **If exists:**
   - Read state file
   - Announce: "Resuming implementation from [current_phase] (Phase [current_phase_index + 1]) - Task [current_task_index + 1]"
   - Skip to indicated phase and task within that phase

   **If not exists:**
   - Create initial state:
     ```json
     {
       "current_phase": "",
       "current_phase_index": 0,
       "current_task_index": 0,
       "completed_phases": [],
       "last_updated": "<current_timestamp>",
       "status": "starting"
     }
     ```

5. **Execute Tasks and Update Track Plan:**

   a. **Announce:** "Executing tasks from plan.md following workflow.md procedures."

   b. **Iterate Through Tasks:** Loop through each task in `plan.md` one by one.

   c. **For Each Task:**
      - **i. Defer to Workflow:** `workflow.md` is the **single source of truth** for task lifecycle. Follow its "Task Workflow" section for implementation, testing, and committing.
      - **ii. Update Implementation State:** After marking task in progress:
        - Set `current_phase` to current phase name
        - Set `current_phase_index` to current phase number (zero-based)
        - Set `current_task_index` to current task number within the phase (zero-based)
        - Set `last_updated` to current timestamp
        - Set `status` to "in_progress"
      - **iii. On Phase Completion:** When all tasks in a phase are complete:
        - Add phase name to `completed_phases` array
        - Reset `current_task_index` to 0
        - Increment `current_phase_index`
        - Update `current_phase` to next phase name

   d. **Handle Blocked Tasks:**
       - If task marked `[!]`:
         > "⚠️ Task is blocked: [reason]"
         > "What would you like to do?"
         > A) Skip this task and continue
         > B) Mark as unblocked and proceed
         > C) Stop implementation here
       - If B: Change `[!]` to `[~]` and proceed

   e. **Self-Check & Issue Handling:**
      - After implementation, run tests, linting, type checks
      - If issues found, analyze the root cause:
      
      **Issue Analysis Decision Tree:**
      
      | Issue Type | Indicators | Action |
      |------------|------------|--------|
      | **Implementation Bug** | Typo, logic error, missing import, test assertion wrong | Fix directly and continue |
      | **Spec Issue** | Requirement wrong, missing, impossible, edge case not covered | Trigger Revise workflow for spec → update spec.md → log in revisions.md → then fix |
      | **Plan Issue** | Missing task, wrong order, task too big/small, dependency missing | Trigger Revise workflow for plan → update plan.md → log in revisions.md → continue |
      | **Blocked** | External dependency, need user input, waiting on API | Mark as blocked, suggest `/conductor-block` |
      
      **Agent MUST announce:** "This issue reveals [spec/plan problem | implementation bug]. [Triggering revision | Fixing directly]."
      
      **For Spec/Plan Issues:**
      1. Create/append to `conductor/tracks/<track_id>/revisions.md` with:
         - Revision number, date, type (Spec/Plan/Both)
         - What triggered the revision
         - Current phase/task when issue occurred
         - Changes made and rationale
      2. Update the relevant document (spec.md or plan.md)
      3. Add "Last Revised" marker at top of updated file
      4. Commit revision before continuing

6. **Finalize Track:**
   - After all tasks complete, update `conductor/tracks.md`: `## [~]` → `## [x]`
   - **Clean Up State:** Delete `conductor/tracks/<track_id>/implement_state.json`
   - Announce track fully complete

---

## 6.0 SYNCHRONIZE PROJECT DOCUMENTATION

**PROTOCOL: Update project-level documentation based on completed track.**

1. **Execution Trigger:** ONLY execute when track reaches `[x]` status. Do NOT execute for other status changes.

2. **Announce:** "Synchronizing project documentation with completed track specifications."

3. **Load Track Specification:** Read `conductor/tracks/<track_id>/spec.md`

4. **Load Project Documents:** Read:
   - `conductor/product.md`
   - `conductor/product-guidelines.md`
   - `conductor/tech-stack.md`

5. **Analyze and Update:**

   **a. Analyze `spec.md`:** Identify new features, functionality changes, or tech stack updates.

   **b. Update `conductor/product.md`:**
   - **Condition:** Determine if completed feature significantly impacts product description
   - **Propose and Confirm:**
     > "Based on the completed track, I propose these updates to `product.md`:"
     > ```diff
     > [Proposed changes in diff format]
     > ```
     > "Do you approve these changes? (yes/no)"
   - **Action:** Only after explicit confirmation, perform edits. Record if changed.

   **c. Update `conductor/tech-stack.md`:**
   - **Condition:** Determine if significant tech stack changes detected
   - **Propose and Confirm:**
     > "Based on the completed track, I propose these updates to `tech-stack.md`:"
     > ```diff
     > [Proposed changes in diff format]
     > ```
     > "Do you approve these changes? (yes/no)"
   - **Action:** Only after explicit confirmation, perform edits. Record if changed.

   **d. Update `conductor/product-guidelines.md` (Strictly Controlled):**
   - **CRITICAL WARNING:** This file defines core identity and communication style. Modify with EXTREME caution.
   - **Condition:** ONLY propose if spec.md explicitly describes branding, voice, tone changes
   - **Propose and Confirm:**
     > "WARNING: The completed track suggests a change to core product guidelines. Please review carefully:"
     > ```diff
     > [Proposed changes in diff format]
     > ```
     > "Do you approve these CRITICAL changes to `product-guidelines.md`? (yes/no)"
   - **Action:** Only after explicit confirmation, perform edits. Record if changed.

6. **Final Report:**
   > "Documentation synchronization complete."
   > - **Changes made to `product.md`:** [description or "No changes needed"]
   > - **Changes made to `tech-stack.md`:** [description or "No changes needed"]
   > - **Changes made to `product-guidelines.md`:** [description or "No changes needed"]

---

## 7.0 TRACK CLEANUP

**PROTOCOL: Offer to archive or delete completed track.**

1. **Execution Trigger:** ONLY execute after track successfully implemented AND documentation sync complete.

2. **Ask for User Choice:**
   > "Track '<track_description>' is now complete. What would you like to do?"
   > A) **Archive** - Move to `conductor/archive/` and remove from tracks file
   > B) **Delete** - Permanently delete folder and remove from tracks file
   > C) **Skip** - Leave in tracks file
   >
   > Please enter A, B, or C.

3. **Handle User Response:**

   **If A (Archive):**
   - Create `conductor/archive/` if not exists
   - Move `conductor/tracks/<track_id>` to `conductor/archive/<track_id>`
   - Remove track section from `conductor/tracks.md`
   - Announce: "Track '<description>' has been successfully archived."

   **If B (Delete):**
   - **CRITICAL WARNING:** Ask final confirmation:
     > "WARNING: This will permanently delete the track folder. This cannot be undone. Are you sure? (yes/no)"
   - If 'yes':
     - Delete `conductor/tracks/<track_id>`
     - Remove track section from `conductor/tracks.md`
     - Announce: "Track '<description>' has been permanently deleted."
   - If 'no':
     - Announce: "Deletion cancelled. Track unchanged."

   **If C (Skip) or other:**
   - Announce: "Completed track will remain in your tracks file."

---

## Status Markers Reference

- `[ ]` - Pending
- `[~]` - In Progress
- `[x]` - Completed
- `[!]` - Blocked
