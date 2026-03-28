# Project Agents

This project uses the following agents:

## Configuration

- Always use **Context7 MCP** when you need library/API documentation, code generation, setup or configuration steps — without the user having to explicitly ask. If Context7 is available and the task involves any library, framework, SDK, API, CLI tool, or cloud service, query Context7 first for current documentation.
- All agents use **model: opus** for maximum capability. Use **sonnet** as a secondary model for cross-validation when noted in workflows.

## Active Agents

### Engineering Core
- [Feature Planner](.claude/agents/engineering/feature-planner.md) — Three-phase validation before implementation (specs, tasks, plan)
- [Clean Code Architect](.claude/agents/engineering/clean-code-architect.md) — Quality code implementation with DRY, SOLID principles
- [DeepDive](.claude/agents/engineering/deepdive.md) — Deep investigation and root cause analysis (research only)
- [DeepCode](.claude/agents/engineering/deepcode.md) — Implementation partner for DeepDive findings
- [Security Vulnerability Scanner](.claude/agents/engineering/security-vulnerability-scanner.md) — Security audits and vulnerability assessment

### Engineering Specialists
- [Backend Architect](.claude/agents/engineering-additional/backend-architect.md) — System design, APIs, databases, scaling
- [Frontend Developer](.claude/agents/engineering-additional/frontend-developer.md) — Modern web UI, Angular, React/Vue, accessibility
- [Mobile App Builder](.claude/agents/engineering-additional/mobile-app-builder.md) — iOS/Android, React Native/Flutter
- [AI Engineer](.claude/agents/engineering-additional/ai-engineer.md) — LLM integration, RAG, Claude MCP
- [DevOps Automator](.claude/agents/engineering-additional/devops-automator.md) — CI/CD, infrastructure, deployment
- [Rapid Prototyper](.claude/agents/engineering-additional/rapid-prototyper.md) — Fast MVP development

### Design
- [UI Designer](.claude/agents/design/ui-designer.md) — Visual design, component libraries, design systems
- [UX Researcher](.claude/agents/design/ux-researcher.md) — User research, usability testing, insights

## Agent Collaboration Patterns

| Workflow | Agents | Description |
|----------|--------|-------------|
| **Bug Investigation → Fix** | DeepDive → DeepCode | DeepDive investigates, DeepCode implements |
| **Security Audit → Remediation** | Security Scanner → DeepCode | Scanner finds issues, DeepCode fixes |
| **Design → Implementation** | UI Designer → Frontend Developer | Design specs to production code |
| **Research → Design → Build** | UX Researcher → UI Designer → Frontend Developer | Full design workflow |
| **Architecture → Implementation** | Backend Architect → Clean Code Architect | Design then quality implementation |
| **Prototype → Production** | Rapid Prototyper → Frontend Developer / Backend Architect | Validate then scale |
| **Feature Planning → Implementation** | Feature Planner → Backend Architect + Frontend Developer | Validate specs, tasks, plan → then implement with domain agents |
| **Feature Planning → Investigation → Fix** | Feature Planner → DeepDive → DeepCode | Plan reveals unknowns, investigate, then implement |
| **Exploratory → Plan → Implementation** | DeepDive → Feature Planner → Implementation agents | Investigate unfamiliar code first, then plan with full context |
| **Full-Stack Feature** | Feature Planner → Backend Architect → Frontend Developer → `/review` → `/security-scan` | End-to-end validated feature delivery |
| **Cross-Model Validation** | Any agent (opus) → re-run validation (sonnet) | Run primary work with opus, cross-check with sonnet for blind spots |

## Multi-Agent & Multi-Model Patterns

### Combining Agents in Flows
Agents and skills can be chained sequentially for comprehensive workflows:

```
/plan-feature [description]          # Feature Planner validates
  → /implement                       # Backend Architect + Frontend Developer build it
                                     # Auto-runs cross-model review (opus + sonnet)
  → /fix-issues                      # Fixes all issues from the review
```

### The Plan → Implement → Fix Cycle
The recommended full-stack workflow:

1. `/plan-feature` — validates specs, tasks, and implementation plan
2. `/implement` — executes the plan with Backend Architect + Frontend Developer, then **automatically** runs cross-model review (opus + sonnet) without asking
3. `/fix-issues` — takes the combined review summary and fixes all must-fix and should-fix items

### Cross-Model Review (built into `/implement`)
After implementation completes, `/implement` automatically:
- Spawns an **opus** review agent and a **sonnet** review agent in parallel
- Both independently review all changes for quality, security, and correctness
- Results are merged, deduplicated, and issues found by both models are flagged as high-confidence
- Produces a combined summary ready for `/fix-issues`

The primary model is always **opus**. Sonnet serves as a cross-reference, not a replacement.

## Recommended Feature Workflow

Choose the right planning approach based on feature size:

| Feature Size | Approach | Example |
|---|---|---|
| **Small** (single task, clear scope) | Normal plan mode — skip the Feature Planner | Add a tooltip, fix a typo, rename a field |
| **Medium-to-large** (multiple tasks, touches data/auth/APIs) | Feature Planner agent — run all 3 phases | New CRUD module, auth flow, payment integration |
| **Exploratory** (unknowns, need to spike first) | DeepDive first → then Feature Planner | Unfamiliar legacy code, unknown API behavior, perf investigation |

For medium-to-large features:

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

## Available Skills

| Skill | Trigger | Purpose |
|---|---|---|
| `/plan-feature` | `/plan-feature [description]` | Run 3-phase Feature Planner validation |
| `/implement` | `/implement [plan or description]` | Execute plan task-by-task with Backend Architect + Frontend Developer, auto-runs `/cross-review` at the end |
| `/cross-review` | `/cross-review [target or blank]` | Dual-model review (opus + sonnet in parallel), merges and deduplicates findings |
| `/fix-issues` | `/fix-issues [specific issues or blank]` | Fix all must-fix and should-fix items from cross-validation summary |
| `/investigate` | `/investigate [problem]` | Deep root cause analysis (DeepDive workflow) |
| `/review` | `/review [file or blank]` | Code review against Clean Code principles (single model) |
| `/security-scan` | `/security-scan [target or blank]` | Security vulnerability scan |
| `/explore` | `/explore [question]` | Quick codebase exploration and understanding |
