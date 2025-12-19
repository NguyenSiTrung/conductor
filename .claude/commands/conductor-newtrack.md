---
description: Create a new feature or bug track with spec and plan
argument-hint: [description]
---

<!-- 
SYSTEM DIRECTIVE: You are an AI agent for the Conductor framework.
CRITICAL: Validate every tool call. If any fails, halt and announce the failure.
-->

# Conductor New Track

Create a new track for: $ARGUMENTS

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

## 2.0 NEW TRACK INITIALIZATION

### 2.1 Get Track Description and Determine Type

1. **Load Project Context:** Read `conductor/` directory files.

2. **Get Track Description:**
   - **If `$ARGUMENTS` provided:** Use it as track description
   - **If empty:** Ask:
     > "Please provide a brief description of the track (feature, bug fix, chore, etc.) you wish to start."
     Wait for response.

3. **Infer Track Type:** Analyze description to classify as "Feature" or "Something Else" (Bug, Chore, Refactor). Do NOT ask user to classify.

---

### 2.2 Interactive Specification Generation (`spec.md`)

1. **Announce:**
   > "I'll now guide you through questions to build a comprehensive `spec.md` for this track."

2. **Questioning Phase (3-5 questions):**

   **Question Classification - CRITICAL:**
   - **1. Classify Question Type:** Before EACH question, classify as:
     - **Additive:** For brainstorming/scope (users, goals, features) - allows multiple answers
     - **Exclusive Choice:** For singular commitments (specific technology, workflow rule) - single answer

   - **2. Formulate Based on Classification:**
     - **If Additive:** Open-ended question + options + "(Select all that apply)"
     - **If Exclusive Choice:** Direct question, do NOT add multi-select

   - **3. Interaction Flow:**
     - **CRITICAL:** Ask ONE question at a time. Wait for response before next question.
     - Last option for every question MUST be "Type your own answer"
     - Confirm understanding by summarizing before moving on

   **If FEATURE (3-5 questions):**
   - Clarifying questions about the feature
   - Implementation approach, interactions, inputs/outputs
   - UI/UX considerations, data involved

   **If SOMETHING ELSE - Bug, Chore, etc. (2-3 questions):**
   - Reproduction steps for bugs
   - Specific scope for chores
   - Success criteria

3. **Draft `spec.md`:** Generate with sections:
   - Overview
   - Functional Requirements
   - Non-Functional Requirements (if any)
   - Acceptance Criteria
   - Out of Scope

4. **User Confirmation:**
   > "I've drafted the specification. Please review:"
   > ```markdown
   > [Drafted spec.md content]
   > ```
   > "Does this accurately capture the requirements? Suggest changes or confirm."

   Revise until confirmed.

---

### 2.3 Interactive Plan Generation (`plan.md`)

1. **Announce:**
   > "Now I will create an implementation plan (`plan.md`) based on the specification."

2. **Generate Plan:**
   - Read confirmed `spec.md` content
   - Read `conductor/workflow.md`
   - Generate hierarchical Phases, Tasks, Sub-tasks
   - **CRITICAL:** Plan structure MUST adhere to workflow methodology (e.g., TDD tasks)
   - Include `[ ]` status markers for each task/sub-task

   **CRITICAL: Inject Phase Completion Tasks**
   - Check if `conductor/workflow.md` defines "Phase Completion Verification and Checkpointing Protocol"
   - If YES, for EACH Phase, append final meta-task:
     ```
     - [ ] Task: Conductor - User Manual Verification '<Phase Name>' (Protocol in workflow.md)
     ```
   - Replace `<Phase Name>` with actual phase name

3. **User Confirmation:**
   > "I've drafted the implementation plan. Please review:"
   > ```markdown
   > [Drafted plan.md content]
   > ```
   > "Does this cover all necessary steps based on spec and workflow? Suggest changes or confirm."

   Revise until confirmed.

---

### 2.4 Create Track Artifacts and Update Main Plan

1. **Check for Duplicate Track Name:**
   - List existing directories in `conductor/tracks/`
   - Extract short names from track IDs (`shortname_YYYYMMDD` → `shortname`)
   - If proposed short name matches existing:
     - **HALT** creation
     - Explain track with that name exists
     - Suggest different name or resuming existing track

2. **Generate Track ID:** Create unique ID: `shortname_YYYYMMDD`

3. **Ask for Priority:**
   > "What priority should this track have?"
   > A) 🔴 Critical - Blocking other work
   > B) 🟠 High - Important, do soon
   > C) 🟡 Medium - Normal priority (default)
   > D) 🟢 Low - Nice to have

   Default to "medium" if skipped.

4. **Ask for Dependencies (Optional):**
   > "Does this track depend on any other tracks being completed first?"
   - If yes: List incomplete tracks from `conductor/tracks.md`, let user select
   - Store selected track_ids in `depends_on` array
   - Default to empty array if skipped or no incomplete tracks

5. **Ask for Time Estimate (Optional):**
   > "Estimated hours to complete? (Enter number or skip)"
   - Store in `estimated_hours` or null if skipped

6. **Create Directory:** `conductor/tracks/<track_id>/`

7. **Create `metadata.json`:**
   ```json
   {
     "track_id": "<track_id>",
     "type": "feature",
     "status": "new",
     "priority": "medium",
     "depends_on": [],
     "estimated_hours": null,
     "created_at": "YYYY-MM-DDTHH:MM:SSZ",
     "updated_at": "YYYY-MM-DDTHH:MM:SSZ",
     "description": "<Initial user description>"
   }
   ```
   Populate with actual values from steps 3-5.

8. **Write Files:**
   - `conductor/tracks/<track_id>/spec.md`
   - `conductor/tracks/<track_id>/plan.md`

9. **Update Tracks File:**
   - Announce: "Updating the tracks file."
   - Append to `conductor/tracks.md`:
     ```markdown

     ---

     ## [ ] Track: <Track Description>
     *Link: [./conductor/tracks/<track_id>/](./conductor/tracks/<track_id>/)*
     ```

10. **Announce Completion:**
    > "New track '<track_id>' has been created and added to the tracks file. Run `/conductor-implement` to start."
