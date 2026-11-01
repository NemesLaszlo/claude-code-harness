# Claude Code Harness

@AGENTS.md

AGENTS.md (imported above) owns the six-beat workflow, the multi-agent rules and the repo-specific sections. This file holds only what Claude Code needs on top of it.

## Rules

- Query **Context7 MCP** before answering anything about a library, framework, SDK, API, CLI tool or cloud service, even without being asked.
- Models: agents that plan, investigate, review or design run on **fable**. Agents that build and edit files run on **opus**. `/review` pins fable on the forked Clean Code Architect. `/cross-review` runs fable and opus side by side. Never downgrade an agent to sonnet or haiku without asking.
- Never start `/implement`, `/ship` or `/greploop` yourself; they are user-invoked (`disable-model-invocation: true`) because they change the repo or push commits in a loop. Tell the user which one to type.
- Never build on `main`. If the branch is `main`, point to `/new-feature` or `claude -w <task>` before editing.
- Apply `/unslop` to every commit message, PR body, doc edit and comment you write.
- After editing this file or AGENTS.md, run `/doctor prompt-audit` to catch conflicts.

## Agents

Research-only agents cannot edit files (`disallowedTools`). Clean Code Architect, Backend Architect and Frontend Developer preload the `code-structure` skill; the other builders do not need the actions/service split.

| Agent | File | Role | Model |
|---|---|---|---|
| Feature Planner | `.claude/agents/engineering/feature-planner.md` | Three-phase plan validation (read-only) | fable |
| Clean Code Architect | `.claude/agents/engineering/clean-code-architect.md` | Quality implementation, refactoring, DeepDive handoffs, .NET 10 | opus (fable under `/review`) |
| DeepDive | `.claude/agents/engineering/deepdive.md` | Root cause analysis (read-only) | fable |
| Security Vulnerability Scanner | `.claude/agents/engineering/security-vulnerability-scanner.md` | Security audit (read-only) | fable |
| Backend Architect | `.claude/agents/engineering-additional/backend-architect.md` | APIs, data, scaling | opus |
| Frontend Developer | `.claude/agents/engineering-additional/frontend-developer.md` | Angular 20+, React, Vue, accessibility | opus |
| Mobile App Builder | `.claude/agents/engineering-additional/mobile-app-builder.md` | React Native, Flutter | opus |
| AI Engineer | `.claude/agents/engineering-additional/ai-engineer.md` | LLM features, Agent SDK, MCP, Claude Code harnesses | opus |
| DevOps Automator | `.claude/agents/engineering-additional/devops-automator.md` | CI/CD, infrastructure, PR checks | opus |
| Rapid Prototyper | `.claude/agents/engineering-additional/rapid-prototyper.md` | MVPs and spikes | opus |
| UI Designer | `.claude/agents/design/ui-designer.md` | Design systems, component specs | fable |
| UX Researcher | `.claude/agents/design/ux-researcher.md` | Research and synthesis (read-only) | fable |

## Which skill runs which agent

| Beat | Skill | Agent |
|---|---|---|
| Isolate | `/new-feature` | main context |
| Plan | `/plan-feature` | forked Feature Planner |
| Plan | `/investigate` | forked DeepDive |
| Plan | `/explore` | forked Explore |
| Build | `/implement` | Backend Architect, Frontend Developer, DevOps Automator, Clean Code Architect per task |
| Review | `/cross-review` | two general-purpose reviewers, fable + opus, launched in parallel |
| Review | `/review` | forked Clean Code Architect, pinned to fable |
| Review | `/fix-issues` | Backend Architect, Frontend Developer, Clean Code Architect per file |
| Prove | `/evidence-driven-testing`, `/before-and-after` | main context |
| Ship | `/ship`, `/greploop` | main context |
| Ship | `/security-scan` | forked Security Vulnerability Scanner |

## Hand-offs between agents

- Bug: DeepDive (`/investigate`) → Clean Code Architect, with the handoff as its brief.
- Security: Security Scanner (`/security-scan`) → `/fix-issues`.
- Design: UX Researcher → UI Designer → Frontend Developer, proven with `/before-and-after`.
- Architecture: Backend Architect → Clean Code Architect.
- Prototype: Rapid Prototyper → Backend Architect or Frontend Developer.
- Unknown code: `/investigate` or `/explore` → `/plan-feature` → `/implement`.

## Sizing the work

| Size | Path |
|---|---|
| Small (one task, obvious scope) | `/new-feature`, build directly, `/review`, `/ship` |
| Medium or large (several tasks, data, auth, APIs) | `/new-feature` → `/plan-feature` → `/implement` → `/fix-issues` → `/evidence-driven-testing` → `/ship` → `/security-scan` |
| Exploratory (unknowns) | `/investigate` or `/explore` first, then the medium path |

Skill details, arguments and requirements live in README.md and each skill's SKILL.md.
