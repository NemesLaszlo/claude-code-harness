---
name: cross-review
description: "Run a cross-model code review using both fable and opus in parallel, then merge and deduplicate findings into a combined summary. Use when the user says cross review, dual review, cross-model review, review with both models, or wants a thorough second-opinion review of changes."
argument-hint: "[file, directory, or blank for current changes]"
allowed-tools: Bash(git branch *), Bash(git diff *), Bash(git log *), Bash(git status *)
---

## Context

- Current branch: !`git branch --show-current || true`
- Staged changes: !`git diff --cached --stat || true`
- Unstaged changes: !`git diff --stat || true`
- Recent commits: !`git log --oneline -5 || true`

## Your Task

Run a **cross-model code review** using both fable and opus to catch blind spots.

**Target:** $ARGUMENTS

If no target specified, review all staged and unstaged changes. If no changes exist, review the most recent commit.

---

### Step 1: Identify What to Review

Determine the review scope:
- If a file or directory is specified → review that
- If there are staged/unstaged changes → review those
- If no changes → review the diff of the last commit (`git diff HEAD~1`)

Collect the full list of files and changes to review.

### Step 2: Launch Parallel Reviews

Use the Agent tool to launch TWO review agents **in parallel** (both in the same message). Both run in the background so the diff never lands in the main context twice.

**Agent A (fable):** Launch a general-purpose agent with `model: fable`. Provide it the list of files/changes and instruct it to review for:
- Clean code principles (naming, function size, duplication, abstraction)
- Actions vs service-layer separation (`/code-structure`): domain rules leaking into services, mechanics duplicated across actions
- Error handling and edge cases
- Security vulnerabilities (injection, auth, data exposure)
- Pattern consistency with the existing codebase
- Performance concerns
- Test coverage gaps and missing evidence for behavior claims

It must produce a structured list of issues with severity, file path, line number, and description.

**Agent B (opus):** Launch a general-purpose agent with `model: opus`. Same files, same review criteria, independent review. This provides a second perspective.

### Step 3: Merge Results

After both agents complete:

1. **Combine** all issues from both reviews into one list
2. **Deduplicate** — match issues that describe the same problem in the same location
3. **Flag consensus** — issues found by BOTH models are high confidence
4. **Flag unique** — issues found by only ONE model are worth reviewing but lower confidence

### Step 4: Produce the Cross-Validation Summary

```
## Cross-Review Summary

**Scope:** [what was reviewed]
**Models:** fable + opus

### Must Fix (found by both models)
- [Issue] — `file:line` — [description]

### Should Fix (found by one model)
- [Issue] — `file:line` — [found by: fable|opus] — [description]

### Consider
- [Suggestion] — `file:line` — [found by: fable|opus] — [description]

### What Looks Good
- [Positive observations agreed on by both models]

### Stats
- Total issues: X (Y must fix, Z should fix, W consider)
- Consensus issues (both models): X
- Fable-only issues: X
- Opus-only issues: X

### Next Step
Run `/fix-issues` to address all must-fix and should-fix items.
```

**IMPORTANT:** Do NOT ask the user whether to run either review. Both models run automatically. This is the entire purpose of this skill.
