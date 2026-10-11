---
name: review
description: "Code review using Clean Code Architect principles. Reviews staged changes, recent commits, or specific files for quality, security, and maintainability. Use when the user says review, review my code, check this, code review, or before committing significant changes."
argument-hint: "[file, directory, or blank for current changes]"
context: fork
agent: clean-code-architect
background: false
allowed-tools: Bash(git branch *), Bash(git diff *), Bash(git log *)
---

## Context

- Current branch: !`git branch --show-current || true`
- Staged changes: !`git diff --cached --stat || true`
- Unstaged changes: !`git diff --stat || true`

## Your Task

Review the following for code quality, security, and maintainability. This is a review, not an implementation task: do not edit files.

**Target:** $ARGUMENTS

If no target is specified, review all staged and unstaged changes. If there are none, review the last commit (`git diff HEAD~1`).

### Review Checklist

**Clean Code**
- [ ] Names are clear and intention-revealing
- [ ] Functions are small and focused (single responsibility)
- [ ] No code duplication
- [ ] Appropriate level of abstraction
- [ ] Code follows existing project patterns and conventions
- [ ] Domain rules live in actions, reusable mechanics in the service layer (`/code-structure`); no god or leaky services

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
- [ ] Prose the change touched (comments, docs, commit message) passes the `/unslop` checklist

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
