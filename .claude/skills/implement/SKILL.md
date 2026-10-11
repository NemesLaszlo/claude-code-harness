---
name: implement
description: "Implement a validated feature plan task-by-task using Backend Architect and Frontend Developer agents, with progress tracking and user confirmation between tasks. After all tasks complete, automatically runs cross-model review (fable + opus). Use when the user says implement, build this, implement the plan, start implementation, or after /plan-feature produces a validated plan."
argument-hint: "[plan or feature description]"
disable-model-invocation: true
allowed-tools: Bash(git branch *), Bash(git log *), Bash(git status *)
---

## Context

- Current branch: !`git branch --show-current || true`
- Recent changes: !`git log --oneline -5 || true`
- Modified files: !`git status --short || true`

## Your Task

You are implementing a validated feature plan **task by task**. Follow this process exactly.

**Plan / Feature:** $ARGUMENTS

If no arguments provided, look for the most recent validated feature plan in this conversation.

---

### Phase 0: Isolation check

If the current branch is `main` or the repo's default branch, stop and tell the user to run `/new-feature` (or start the session with `claude -w <task>`) first. Never build on `main`.

### Phase 1: Preparation

1. Extract all tasks from the validated plan in implementation order
2. For each task, determine the assigned agent:
   - **Backend tasks** (APIs, database, services, middleware, auth logic, data models, migrations) → **Backend Architect** agent
   - **Frontend tasks** (components, UI, forms, routing, state management, styling) → **Frontend Developer** agent
   - **Infrastructure tasks** (CI/CD, Docker, deployment) → **DevOps Automator** agent
   - **General code quality tasks** (refactoring, shared utilities, cross-cutting concerns) → **Clean Code Architect** agent

3. Present the full task list as a table before starting:

```
## Implementation Plan

| # | Task | Agent | Status |
|---|------|-------|--------|
| 1 | [task description] | Backend Architect | Pending |
| 2 | [task description] | Frontend Developer | Pending |
| ... | ... | ... | ... |
```

Ask the user to confirm the plan before proceeding.

---

### Phase 2: Task-by-Task Implementation

For each task **in order**:

1. **Announce** which task you're starting and which agent is handling it
2. **Capture the before state** when the task fixes a bug or changes visible behavior: reproduce it and save the capture under `.artifacts/<task>/` before touching code. It becomes the "before" half of the PR evidence.
3. **Execute** the task using the assigned agent:
   - Use existing codebase patterns — read before writing
   - Keep domain rules in actions and reusable mechanics in the service layer (`/code-structure`)
   - Respect task dependencies — don't start a task until its dependencies are complete
   - Run tests after each task if tests exist
4. **Summarize** what was done by showing the updated progress table:

```
## Progress after Task #X: [task name]

### Changes Made
- `file/path.ext` — [what changed]
- `file/path.ext` — [what changed]

### Execution Summary

| # | Task | Agent | Status |
|---|------|-------|--------|
| 1 | [task description] | Backend Architect | Done |
| 2 | [task description] | Frontend Developer | Done |
| 3 | [task description] | Backend Architect | **Current** |
| 4 | [task description] | Frontend Developer | Pending |
| ... | ... | ... | ... |
```

5. **Ask the user** to confirm before proceeding to the next task: _"Ready for Task #X+1?"_

**If blocked on a task:** Report the blocker, skip to the next independent task, and note the skipped task.

---

### Phase 3: Cross-Model Review (automatic — do NOT skip or ask)

After ALL implementation tasks are complete, automatically run `/cross-review` to perform the dual-model review (fable + opus in parallel).

Before invoking, show the final implementation summary table:

```
## Implementation Completed

| # | Task | Agent | Files Changed |
|---|------|-------|---------------|
| 1 | [task] | Backend Architect | `file1.ext`, `file2.ext` |
| 2 | [task] | Frontend Developer | `file3.ext` |
| ... | ... | ... | ... |
```

Then run `/cross-review` which will produce the combined cross-validation summary.

**IMPORTANT:** Do NOT ask the user whether to run the review. Do NOT skip the review. The cross-model review is a mandatory part of this skill.

### Phase 4: Hand-off

Close with the next beats of the workflow:

1. `/fix-issues` for the must-fix and should-fix items
2. `/evidence-driven-testing` to prove the change works (recording, scripted captures, or measured numbers)
3. `/ship` to open the PR with the evidence, then `/security-scan` before deploying
