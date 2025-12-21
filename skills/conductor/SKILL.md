---
name: conductor
description: |
  Context-driven development methodology for organized, spec-first coding. Use when:
  - Project has a `conductor/` directory
  - User mentions specs, plans, tracks, or context-driven development
  - Files like `conductor/tracks.md`, `conductor/product.md`, `conductor/workflow.md` exist
  - User asks about project status, implementation progress, or track management
  - User wants to organize development work with TDD practices
  - User invokes `/conductor-*` commands (setup, newtrack, implement, status, revert, validate, block, skip, archive, export)
  
  Interoperable with Gemini CLI extension and Claude Code commands.
---

# Conductor: Context-Driven Development

Measure twice, code once.

## Overview

Conductor enables context-driven development by:
1. Establishing project context (product vision, tech stack, workflow)
2. Organizing work into "tracks" (features, bugs, improvements)
3. Creating specs and phased implementation plans
4. Executing with TDD practices and progress tracking

## Context Loading

When this skill activates, load these files to understand the project:
1. `conductor/product.md` - Product vision and goals
2. `conductor/tech-stack.md` - Technology constraints
3. `conductor/workflow.md` - Development methodology (TDD, commits)
4. `conductor/tracks.md` - Current work status

For active tracks, also load:
- `conductor/tracks/<track_id>/spec.md`
- `conductor/tracks/<track_id>/plan.md`

## Proactive Behaviors

1. **On new session**: Check for in-progress tracks, offer to resume
2. **On task completion**: Suggest next task or phase verification
3. **On blocked detection**: Alert user and suggest alternatives
4. **On all tasks complete**: Congratulate and offer archive/cleanup

## Intent Mapping

| User Intent | Command |
|-------------|---------|
| "Set up this project" | `/conductor-setup` |
| "Create a new feature" | `/conductor-newtrack [desc]` |
| "Start working" / "Implement" | `/conductor-implement [id]` |
| "What's the status?" | `/conductor-status` |
| "Undo that" / "Revert" | `/conductor-revert` |
| "Check for issues" | `/conductor-validate` |
| "This is blocked" | `/conductor-block` |
| "Skip this task" | `/conductor-skip` |
| "Archive completed" | `/conductor-archive` |
| "Export summary" | `/conductor-export` |

## References

- **Detailed workflows**: [references/workflows.md](references/workflows.md) - Step-by-step command execution
- **Directory structure**: [references/structure.md](references/structure.md) - File layout and status markers
