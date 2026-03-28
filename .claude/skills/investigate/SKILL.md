---
name: investigate
description: "Deep investigation and root cause analysis using the DeepDive agent workflow. Use when the user says investigate, why does this break, what is causing, debug this, find the root cause, or any request to analyze a bug or issue without immediately writing a fix."
---

## Context

- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`
- Modified files: !`git status --short`

## Your Task

You are invoking the **DeepDive** agent workflow. Investigate the following:

**Problem:** $ARGUMENTS

### Investigation Process

1. **Get your bearings** — Understand the project structure, tech stack, and relevant documentation
2. **Understand the problem** — What's the symptom? What's the scope? What does success look like?
3. **Investigate deeply** — Explore the codebase, trace data flows, check logs, follow the chain from input to failure
4. **Analyze and form conclusions** — Identify root cause (or top candidates), trace the chain, consider edge cases, evaluate solution options

### Output

Produce a structured **DeepDive Handoff** with:

- **Task**: The original problem
- **Summary**: 1-2 sentence overview
- **Root Cause Analysis**: Where (file paths, line numbers), What (exact issue), Why (how it causes the problem)
- **Evidence**: Specific log entries, error messages, code snippets found
- **Recommended Fix**: What needs to change (describe, don't implement)
- **Alternative Approaches**: Options with pros/cons
- **Things to Watch Out For**: Gotchas and edge cases
- **Files to Modify**: List with what needs doing in each
- **Files for Reference**: Useful patterns, don't modify
- **Open Questions**: Anything uncertain
- **How to Verify**: How to test that a fix works

Do NOT write code or implement fixes. Produce analysis that an implementation agent (DeepCode, Clean Code Architect) can act on.
