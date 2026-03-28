---
name: fix-issues
description: "Fix all issues from a cross-validation review summary. Use when the user says fix issues, fix these, fix the review findings, address the issues, or after /implement produces a cross-validation summary."
---

## Context

- Current branch: !`git branch --show-current`
- Modified files: !`git status --short`
- Staged changes: !`git diff --cached --stat`

## Your Task

Fix all issues identified in the most recent cross-validation summary or code review.

**Specific issues to fix:** $ARGUMENTS

If no arguments provided, find the most recent cross-validation summary or `/review` output in this conversation and address all **Must Fix** and **Should Fix** items.

---

### Fix Process

**Step 1: Parse the issues**

Extract all issues from the review summary. Categorize by file and type:
- Security issues (fix first)
- Bug/correctness issues
- Error handling gaps
- Code quality issues
- Performance issues

**Step 2: Fix in order**

For each issue:
1. Read the file and understand the surrounding code
2. Apply the fix following existing codebase patterns
3. Verify the fix doesn't break adjacent code
4. Move to the next issue

**Agent Assignment:**
- Backend file issues → use **Backend Architect** agent
- Frontend file issues → use **Frontend Developer** agent
- Cross-cutting or refactoring → use **Clean Code Architect** agent
- Security-specific fixes → use **Security Vulnerability Scanner** agent for guidance, then implement

**Fix Rules:**
- Fix the actual issue — don't just suppress warnings
- Keep changes minimal and focused on the reported issue
- Don't refactor surrounding code unless it's part of the fix
- Follow existing patterns in the file
- If a fix requires changes in multiple files, make all related changes together

**Step 3: Verify**

After all fixes are applied:
1. Run tests if they exist
2. Do a quick scan of all modified files to ensure no regressions

**Step 4: Report**

```
## Fix Summary

### Issues Fixed
- [X/Y] Must Fix items resolved
- [X/Y] Should Fix items resolved
- [X/Y] Consider items addressed (if any)

### Changes Made
- `file:line` — [what was fixed]

### Issues Not Fixed (if any)
- [Issue] — [reason: needs human decision / out of scope / blocked by X]

### Verification
- Tests: [passed/failed/no tests]
- Regression check: [clean/issues found]
```
