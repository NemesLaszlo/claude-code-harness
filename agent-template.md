---
name: [agent-name]
description: [One-line description of when to use this agent. Claude reads this to decide when to delegate, so write it trigger-first: "Use this agent when..."]
version: 1.0
model: fable
color: blue
# --- Optional fields (delete the ones you don't need) ---
# tools: Read, Grep, Glob, Bash            # allowlist; omit to inherit every tool
# disallowedTools: Edit, Write, NotebookEdit   # denylist; use for research-only agents
# skills: code-structure                   # preload full skill content at startup (max 32)
# permissionMode: default                  # default | acceptEdits | plan | dontAsk | bypassPermissions
# isolation: worktree                      # run in a throwaway git worktree, removed if unchanged
# memory: project                          # persistent memory dir: user | project | local
# effort: high                             # low | medium | high | xhigh | max
# maxTurns: 50
# background: true                         # always run in the background
---

# [Emoji] [Agent Name] Agent

## Purpose

[2-3 sentences describing who this agent is and what they excel at. Write in second person ("You are..."). Include the core value they provide.]

## Core Responsibilities

### [Responsibility Category 1]
- [Specific task or behavior]
- [Another task]
- [Continue as needed]

### [Responsibility Category 2]
- [Tasks in this category]

[Add 3-5 responsibility categories]

## Key Skills

- **[Skill Category]:** [Specific tools, technologies, methods]
- [Add 5-8 skill categories]

## Workflow fit

- [Which beat of AGENTS.md this agent serves: Isolate, Plan, Build, Review, Prove, Ship]
- [Which skills invoke it, and which agent it hands off to]

## Communication Style

- [How the agent communicates]
- [Tone and approach]
- [What to emphasize]
- [Add 4-6 style points]
