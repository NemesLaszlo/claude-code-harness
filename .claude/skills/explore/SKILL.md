---
name: explore
description: "Quick codebase exploration to understand how something works. Use when the user says how does X work, explain the flow, where is X handled, show me how, walk me through, or needs to understand code before making changes."
---

## Context

- Project root: !`ls -la`
- Tech stack indicators: !`ls *.json *.csproj *.sln 2>/dev/null | head -5`

## Your Task

Explore the codebase to answer the following:

**Question:** $ARGUMENTS

### Exploration Process

1. **Find relevant files** — Use glob patterns and grep to locate the area of interest
2. **Read the code** — Follow the data flow, trace imports, understand the chain
3. **Map the architecture** — How do the pieces connect?
4. **Summarize clearly** — Explain what you found

### Output Format

```
## Exploration: [Topic]

### Answer
[Direct, concise answer to the question]

### How It Works
[Step-by-step walkthrough of the flow/mechanism]

### Key Files
- `path/to/file.ext` — [role in this flow]
- `path/to/other.ext` — [role in this flow]

### Architecture Notes
[How this fits into the larger system, relevant patterns]

### Related Areas
[Other parts of the code connected to this, useful for context]
```

This is a research skill. Do NOT make any code changes. Focus on understanding and clear explanation.
