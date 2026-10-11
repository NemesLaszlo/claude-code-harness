---
name: ship
description: Finish a task and open the pull request. Runs the repo's checks, assembles before/after evidence, commits with a human-sounding message, rebases onto origin/main, pushes, opens the PR with an evidence-backed body, and hands off to the Greptile loop when it is installed. Use when the user says ship, open the PR, finish the task, or ready to merge.
argument-hint: "[pr-title]"
disable-model-invocation: true
allowed-tools: Bash(git status *), Bash(git diff *), Bash(git log *), Bash(git branch *), Bash(git fetch *), Bash(git rev-parse *), Bash(gh pr list *), Bash(gh pr view *), Bash(gh pr checks *)
---

## Context

- Branch: !`git branch --show-current || true`
- Default branch: !`git symbolic-ref --short refs/remotes/origin/HEAD 2>/dev/null || echo origin/main`
- Status: !`git status --short || true`
- Commits ahead of origin/main: !`git log --oneline origin/main..HEAD 2>/dev/null || true`
- Existing PR for this branch: !`gh pr view --json number,url,title 2>/dev/null || echo "none"`

## Your task

Take the current branch from "code works on my machine" to an open pull request with proof. Proposed title: **$ARGUMENTS** (derive one from the commits if empty).

Work through the beats in order. Stop and report instead of guessing whenever a step fails.

### 1. Preconditions

- Refuse to continue if the branch is `main` or the default branch. Point at `/new-feature`.
- Confirm the diff is limited to the assigned task. Unrelated changes get a separate branch.
- Run the repo's checks. Use the exact commands listed in `AGENTS.md` or `CLAUDE.md` under *Commands and checks*; if none are listed, detect them (`npm test`, `dotnet test`, `pytest`, `ng test`, lint, typecheck, build) and say which ones you ran. Failing checks block the PR.

### 2. Evidence

- Collect what `/evidence-driven-testing` produced under `.artifacts/<task>/`. If nothing exists yet, capture it now: a before/after pair for anything with a visible surface, measured numbers or output pairs for anything without one.
- For UI changes, build the markdown table with `/before-and-after` (`--markdown` uploads the pair). Existing PNGs from the evidence run can be reused as-is.
- Every claim in the PR body must point at a capture, a number, or a transcript excerpt. No "tested manually" without proof.

### 3. Commit and rebase

- Stage only the task's files. Write the commit message to the `/unslop` rules: plain words, active voice, no em dashes, say what changed and why.
- `git fetch origin` then `git rebase origin/main`. Regenerate lockfiles on conflict instead of hand-merging. If a conflict cannot be resolved confidently, stop and report.
- Rerun the checks after the rebase.

### 4. Push

- First push: `git push -u origin <branch>`.
- After rebasing an already-pushed branch: `git push --force-with-lease`. Never plain `--force`, never to `main`.

### 5. Open the PR

Use `gh pr create` (or update the existing PR with `gh pr edit`). Body template:

```
## What changed
[One paragraph: the change and the reason.]

## How it was tested
- [Check command] passed
- [Flow verified] with evidence: [link or path]

## Before / after
[Table from /before-and-after, or measured numbers / output pairs]

## Risks and follow-ups
- [Anything reviewers should weigh]
```

Run the title and body through the `/unslop` checklist before posting. Append the attribution lines the session requires.

### 6. Automated review loop

- If Greptile is installed on the repo (a `greptile` check run exists or the bot has commented on a previous PR), tell the user to run `/greploop` (or `/greploop-apps` for very large PRs). Both are user-invoked because they push commits in a loop.
- Otherwise rely on the `/cross-review` and `/fix-issues` pass that `/implement` already ran, and say so in the hand-off.

### 7. Hand off

End by presenting the PR URL, the checks that passed, and where the evidence lives. Do not merge. Keep the worktree until the PR is merged or closed.
