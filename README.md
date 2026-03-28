# Agent Collection

A curated library of **13 specialized Claude AI agents** and **8 skills** for production software development. Copy the `.claude/` folder into any project and start using agents and slash commands immediately.

## Quick Start

```bash
# Copy the .claude folder into your project
cp -r Agent-Collection/.claude/ your-project/.claude/

# Optionally copy the user-level skill to your home directory (available across all projects)
cp -r Agent-Collection/user-skills/* ~/.claude/skills/
```

That's it. Agents and skills are ready to use. Reference them in your project's `CLAUDE.md` — see [`CLAUDE.md`](CLAUDE.md) in this repo for a complete example you can adapt.

### What Gets Copied

```
your-project/
├── .claude/
│   ├── agents/
│   │   ├── engineering/              # Core engineering agents
│   │   │   ├── feature-planner.md
│   │   │   ├── clean-code-architect.md
│   │   │   ├── deepdive.md
│   │   │   ├── deepcode.md
│   │   │   └── security-vulnerability-scanner.md
│   │   ├── engineering-additional/   # Specialist agents
│   │   │   ├── backend-architect.md
│   │   │   ├── frontend-developer.md
│   │   │   ├── mobile-app-builder.md
│   │   │   ├── ai-engineer.md
│   │   │   ├── devops-automator.md
│   │   │   └── rapid-prototyper.md
│   │   └── design/                   # Design agents
│   │       ├── ui-designer.md
│   │       └── ux-researcher.md
│   └── skills/                       # Slash command skills
│       ├── plan-feature/
│       ├── implement/
│       ├── cross-review/
│       ├── fix-issues/
│       ├── investigate/
│       ├── review/
│       ├── security-scan/
│       └── explore/
└── CLAUDE.md                         # Reference the agents here
```

## Agents

All agents use **Claude Opus** as the primary model.

### Engineering Core

| Agent | Purpose | Best For |
|-------|---------|----------|
| **Feature Planner** | Three-phase validation before implementation | Feature planning, spec validation, edge case analysis, risk assessment |
| **Clean Code Architect** | Quality code with DRY, SOLID principles | Feature implementation, refactoring, reusable modules |
| **DeepDive** | Deep investigation and root cause analysis | Bug investigation, performance issues, architectural problems |
| **DeepCode** | Implementation partner for DeepDive | Executing fixes based on investigation findings |
| **Security Vulnerability Scanner** | Security audits and vulnerability assessment | OWASP Top 10, penetration testing, security reviews |

### Engineering Specialists

| Agent | Purpose | Best For |
|-------|---------|----------|
| **Backend Architect** | System design, APIs, databases, scaling | API design, database architecture, microservices, .NET/Node/Go/Python |
| **Frontend Developer** | Modern web UI, accessibility, performance | Angular (17+), React, Vue, component architecture, Core Web Vitals |
| **Mobile App Builder** | iOS/Android, cross-platform apps | React Native, Flutter, native integrations |
| **AI Engineer** | LLM integration, RAG, Claude MCP | AI features, embeddings, agentic workflows |
| **DevOps Automator** | CI/CD, infrastructure, deployment | Pipelines, Kubernetes, IaC, monitoring |
| **Rapid Prototyper** | Fast MVP development | Hackathons, proof-of-concepts, quick validation |

### Design

| Agent | Purpose | Best For |
|-------|---------|----------|
| **UI Designer** | Visual design, component libraries | Design systems, component specs, visual polish |
| **UX Researcher** | User research, usability testing | User interviews, journey maps, research synthesis |

## Skills (Slash Commands)

Skills are one-tap shortcuts that invoke agent workflows.

| Skill | Command | What It Does |
|-------|---------|-------------|
| **plan-feature** | `/plan-feature [description]` | Runs all 3 Feature Planner validation phases and produces an implementation-ready plan |
| **implement** | `/implement [plan or description]` | Executes plan task-by-task with Backend Architect + Frontend Developer (asks before each task), auto-runs `/cross-review` at the end |
| **cross-review** | `/cross-review` or `/cross-review src/` | Dual-model review — runs opus and sonnet in parallel, merges and deduplicates findings into a combined summary |
| **fix-issues** | `/fix-issues` | Fixes all must-fix and should-fix items from the cross-validation summary |
| **investigate** | `/investigate [problem]` | Deep codebase investigation and root cause analysis (DeepDive workflow) |
| **review** | `/review` or `/review src/auth/` | Single-model code review against Clean Code Architect principles |
| **security-scan** | `/security-scan` or `/security-scan src/api/` | Security vulnerability scan — OWASP Top 10, injection, auth, data exposure |
| **explore** | `/explore [question]` | Quick codebase exploration — trace flows, map architecture, understand code |

### User-Level Skill

Copy to `~/.claude/skills/` to make it available across all projects:

| Skill | Command | What It Does |
|-------|---------|-------------|
| **setup-agents** | `/setup-agents [project-type]` | Analyzes project, recommends agents, copies files, generates CLAUDE.md section |

## Agent Collaboration Flows

Agents are designed to chain together. The **Backend Architect** and **Frontend Developer** are the primary implementation agents — most skill flows route through them.

### Core Flows

```
Bug Investigation → Fix
  DeepDive → DeepCode

Security Audit → Remediation
  Security Scanner → DeepCode

Full-Stack Feature (recommended)
  /plan-feature → /implement → /fix-issues → /security-scan

Architecture → Implementation
  Backend Architect → Clean Code Architect

Design → Implementation
  UI Designer → Frontend Developer

Research → Design → Build
  UX Researcher → UI Designer → Frontend Developer

Prototype → Production
  Rapid Prototyper → Frontend Developer / Backend Architect

Exploratory → Plan → Build
  DeepDive → /plan-feature → /implement → /fix-issues
```

### The Plan → Implement → Fix Cycle

The primary skill chain for any non-trivial feature:

```
/plan-feature add user authentication
  → /implement                    # Backend Architect + Frontend Developer build it
                                  # Cross-model review runs automatically (opus + sonnet)
  → /fix-issues                   # Fixes all issues from the combined review
  → /security-scan                # Final security check before deploy
```

Other common chains:

```
/investigate why login fails after token refresh
  → DeepCode implements the fix
  → /review validates the changes
```

```
/explore how does the payment flow work
  → /plan-feature add subscription tier support
  → /implement → /fix-issues
```

### Cross-Model Review (`/cross-review`)

`/cross-review` runs opus and sonnet in parallel for a dual-perspective review. It's:
- **Built into `/implement`** — runs automatically after all tasks complete
- **Usable standalone** — run `/cross-review` anytime on any changes (after `/fix-issues`, after manual edits, etc.)

How it works:
- **Opus** reviews all changes for quality, security, and correctness
- **Sonnet** reviews the same changes independently for a second perspective
- Results are merged, deduplicated, and consensus issues (found by both) are flagged high-confidence
- The combined summary feeds directly into `/fix-issues`

The primary model is always **opus**. Sonnet serves as a cross-reference to catch blind spots.

## Recommended Workflow by Feature Size

| Feature Size | Approach | Example |
|---|---|---|
| **Small** (single task, clear scope) | Just do it — skip Feature Planner | Add a tooltip, fix a typo, rename a field |
| **Medium-to-large** (multiple tasks, data/auth/APIs) | `/plan-feature` → `/implement` → `/fix-issues` | New CRUD module, auth flow, payment integration |
| **Exploratory** (unknowns, spike needed) | `/investigate` → `/plan-feature` → `/implement` | Unfamiliar legacy code, unknown API behavior |

### Full Feature Lifecycle

```
1. Feature Idea
   ↓
2. /plan-feature [description]
   Phase 1: Domain & Spec Validation
   Phase 2: Task Validation
   Phase 3: Implementation Plan Validation
   ↓
3. /implement
   → Backend Architect + Frontend Developer execute tasks
   → Cross-model review runs automatically (opus + sonnet)
   → Combined review summary produced
   ↓
4. /fix-issues
   → Fixes all must-fix and should-fix items
   ↓
5. /security-scan before deploying
```

## Recommended Agent Sets by Project Type

| Project Type | Agents |
|---|---|
| **.NET + Angular Full-Stack** | Feature Planner, Backend Architect, Frontend Developer, Clean Code Architect, DevOps Automator, Security Scanner |
| **Backend API** | Feature Planner, Backend Architect, Clean Code Architect, Security Scanner, DevOps Automator |
| **Frontend SPA** | Feature Planner, Frontend Developer, UI Designer, Clean Code Architect |
| **Mobile App** | Feature Planner, Mobile App Builder, UI Designer, Backend Architect |
| **Startup / MVP** | Feature Planner, Rapid Prototyper, Backend Architect, UI Designer |
| **Enterprise** | Feature Planner, DeepDive, DeepCode, Backend Architect, Security Scanner, DevOps Automator |
| **AI / ML** | Feature Planner, AI Engineer, Backend Architect, Clean Code Architect |

**Feature Planner** is included in every set. Consider adding **DeepDive + DeepCode** to any project where you'll need bug investigation.

## Creating Custom Agents

Use the provided template:

```bash
cp agent-template.md .claude/agents/your-domain/your-agent.md
```

See [agent-template.md](agent-template.md) for the structure.

## License

See [LICENSE](LICENSE) for details.
