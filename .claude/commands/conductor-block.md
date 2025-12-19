---
description: Mark a task as blocked with a reason
---

# Conductor Block

Mark a task as blocked.

## 1. Identify Task
- If argument provided, find that task
- Otherwise show in-progress tasks for selection

## 2. Get Reason
Ask for the blocking reason.

## 3. Update Plan
Change `[~]` to `[!]` and append `[BLOCKED: reason]`

## 4. Confirm
Announce task is blocked.
