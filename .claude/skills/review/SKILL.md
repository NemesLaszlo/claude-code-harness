---
name: review
description: "Code review using Clean Code Architect principles. Reviews staged changes, recent commits, or specific files for quality, security, and maintainability. Use when the user says review, review my code, check this, code review, or before committing significant changes."
---

## Context

- Current branch: !`git branch --show-current`
- Staged changes: !`git diff --cached --stat`
- Unstaged changes: !`git diff --stat`

## Your Task

Review the following for code quality, security, and maintainability:

**Target:** $ARGUMENTS

If no target is specified, review all staged and unstaged changes.

### Review Checklist

**Clean Code**
- [ ] Names are clear and intention-revealing
- [ ] Functions are small and focused (single responsibility)
- [ ] No code duplication
- [ ] Appropriate level of abstraction
- [ ] Code follows existing project patterns and conventions

**Quality**
- [ ] Error handling is comprehensive and graceful
- [ ] Edge cases are handled (null, empty, boundary conditions)
- [ ] No debugging artifacts left behind (console.log, TODO hacks)
- [ ] Dependencies properly managed
- [ ] Performance considerations addressed where relevant

**Security**
- [ ] No injection vulnerabilities (SQL, command, XSS)
- [ ] Input validation at system boundaries
- [ ] Sensitive data not exposed (logs, error messages, responses)
- [ ] Auth/authz checked where needed
- [ ] No hardcoded secrets or credentials

**Maintainability**
- [ ] Code is testable with clear boundaries
- [ ] Changes are backwards compatible (or breaking changes documented)
- [ ] Cognitive complexity is low (minimal nesting, clear flow)

### Output Format

```
## Code Review

### Summary
[1-2 sentence overview of what was reviewed]

### Issues Found

**Must Fix:**
- [Issue] — `file:line` — [why it matters]

**Should Fix:**
- [Issue] — `file:line` — [why it matters]

**Consider:**
- [Suggestion] — `file:line` — [potential improvement]

### What Looks Good
- [Positive observation]

### Verdict: [APPROVE | APPROVE WITH COMMENTS | REQUEST CHANGES]
```

Be specific with file paths and line numbers. Focus on substance, not style nitpicks.
