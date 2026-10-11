---
name: new-feature
description: Start a task in an isolated Git worktree branched from origin/main so parallel agents or sessions never collide on the same checkout. Use when starting a feature, fix, or refactor that must not touch main, when several agents share one repo, or when the user says new feature, start a task, isolate this, or new branch for this.
argument-hint: "[task-name]"
allowed-tools: Bash(git fetch *), Bash(git status *), Bash(git branch *), Bash(git worktree list*), Bash(gh pr list *), Bash(gh pr diff *)
---

## Context

- Current branch: !`git branch --show-current || true`
- Uncommitted work: !`git status --short || true`
- Existing worktrees: !`git worktree list || true`
- Open PRs: !`gh pr list --limit 20 2>/dev/null || echo "gh unavailable or not a GitHub repo"`

## Your task

Isolate the task **$ARGUMENTS** in its own worktree and branch, created from the latest `origin/main`. Never build on `main`, and never reuse another agent's worktree or branch.

### Harness deltas (read first)

- **Claude Code** manages worktrees natively. Prefer the native path and skip the manual `git worktree add`:
  - From the terminal: `claude --worktree <task-name>` (or `claude -w <task-name>`). The checkout lands in `.claude/worktrees/<task-name>/` on branch `worktree-<task-name>`. `claude -w "#1234"` or a PR URL checks out that PR.
  - From inside a session: use the `EnterWorktree` tool with the task name. Leave with `ExitWorktree`.
  - Subagents can get a throwaway worktree with `isolation: worktree` in their frontmatter.
  - Keep the harness-assigned branch name. Make sure `.claude/worktrees/` is gitignored (this collection's `.gitignore` already does that). Put gitignored files that a worktree needs, such as `.env`, in a `.worktreeinclude` file so the harness copies them.
- **Cursor-managed worktrees** (branches named `worktree-*`): keep the assigned branch and worktree; run steps 1, 2 and 5 only.
- **Any other harness**: follow all steps.

### Steps

1. **Sync**: `git fetch origin`.

2. **Scope check**: skim open PRs' changed files (`gh pr diff <n> --name-only`). If this task needs files another open PR is editing, **stop and ask for direction**. Also check for uncommitted work in the shared checkout; another agent may be mid-task.

3. **Name the task**: lowercase-with-hyphens plus a short unique suffix, e.g. `user-auth-0816a`. If the name already exists, pick another; never force or reuse.

4. **Create the worktree** (manual harnesses only):

   ```bash
   git worktree add .claude/worktrees/<task-name> -b agent/<task-name> origin/main
   ```

5. **Enter and verify**:

   ```bash
   cd .claude/worktrees/<task-name>
   git branch --show-current   # must print your task branch, not main
   ```

   Install dependencies fresh inside the worktree (worktrees don't share `node_modules`, `bin/obj`, or virtualenvs) and confirm the runtime version the repo requires before running anything.

### Remember

- Worktrees do **not** isolate shared resources: dev-server ports, shared databases, and lockfiles are global. Confirm a port answers *your* process before trusting it, and resolve lockfile conflicts by regenerating, never by hand-merging.
- Keep the worktree until the PR is merged or closed. Claude Code removes unnamed, unchanged worktrees itself and asks before removing ones with changes. Manual cleanup after merge:

  ```bash
  git worktree remove .claude/worktrees/<task-name>
  git branch -D agent/<task-name>   # -D is expected after a squash or rebase merge
  ```

### Next

Small change: build it directly. Anything touching data, auth, APIs, or several files: run `/plan-feature` first, then `/implement`.
