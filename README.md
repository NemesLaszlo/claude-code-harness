# Agent Collection

A curated harness for Claude Code: **13 specialized agents**, **16 skills**, and a six-beat **AGENTS.md workflow** that takes a task from an isolated worktree to an evidence-backed pull request. Copy the `.claude/` folder and `AGENTS.md` into any project and start working immediately.

## Quick Start

```bash
# Copy the harness into your project
cp -r Agent-Collection/.claude/ your-project/.claude/
cp Agent-Collection/AGENTS.md your-project/AGENTS.md

# Optionally install the user-level setup skill (available across all projects)
cp -r Agent-Collection/user-skills/* ~/.claude/skills/
```

Then add `@AGENTS.md` to your project's `CLAUDE.md` and fill in the repo-specific sections at the bottom of `AGENTS.md` (checks, invariants, environment). See this repo's [`CLAUDE.md`](CLAUDE.md) for a complete example. Or run `/setup-agents` inside the project and let it do the copying and pruning.

### What Gets Copied

```
your-project/
├── AGENTS.md                         # Six-beat workflow, multi-agent rules, repo-specific sections
├── CLAUDE.md                         # @AGENTS.md import + agent/skill reference
└── .claude/
    ├── settings.json                 # Pre-approved read-only git/gh commands, force-push denied
    ├── agents/
    │   ├── engineering/              # feature-planner, clean-code-architect, deepdive, deepcode, security-vulnerability-scanner
    │   ├── engineering-additional/   # backend-architect, frontend-developer, mobile-app-builder, ai-engineer, devops-automator, rapid-prototyper
    │   └── design/                   # ui-designer, ux-researcher
    └── skills/
        ├── new-feature/              # Isolate
        ├── plan-feature/ investigate/ explore/          # Plan
        ├── implement/ code-structure/                   # Build
        ├── cross-review/ fix-issues/ review/            # Review
        ├── evidence-driven-testing/ before-and-after/   # Prove
        ├── ship/ greploop/ greploop-apps/ security-scan/ # Ship
        └── unslop/                   # Always: text written for people
```

## The Workflow

Every task moves through six beats. `AGENTS.md` describes them for any coding agent; the skills make them one command each in Claude Code.

| # | Beat | Skill | What happens |
|---|------|-------|--------------|
| 1 | **Isolate** | `/new-feature` | Worktree and branch from `origin/main` (`claude -w <task>` or `EnterWorktree`), scope check against open PRs |
| 2 | **Plan** | `/plan-feature`, `/investigate`, `/explore` | Feature Planner validates specs, tasks, plan and evidence plan. Forked subagents keep the main context clean |
| 3 | **Build** | `/implement` + `/code-structure` | Backend Architect and Frontend Developer execute task by task. Actions own domain rules, a service layer owns reusable mechanics |
| 4 | **Review** | `/cross-review` → `/fix-issues` | Automatic after `/implement`: fable and opus review independently, consensus findings are must-fix |
| 5 | **Prove** | `/evidence-driven-testing`, `/before-and-after` | Annotated recording of the live test session, scripted captures when headless, measured numbers for non-UI work |
| 6 | **Ship** | `/ship` → `/greploop` → `/security-scan` | Checks, evidence assembly, commit, rebase, push, PR with before/after proof, Greptile loop to 5/5, security scan before deploy |

```
/new-feature add-subscriptions
/plan-feature add subscription tier support
  → /implement                    # builds, then auto cross-review (fable + opus)
  → /fix-issues                   # resolves must-fix and should-fix
  → /evidence-driven-testing      # proves it with a recording or captures
  → /ship                         # opens the PR with the evidence
  → /greploop                     # when Greptile is installed
  → /security-scan                # before deploy
```

Small change? `/new-feature`, build directly, `/review`, `/ship`. Unknown territory? `/investigate` or `/explore` first.

## Agents

All agents use **Claude Fable** as the primary model. Research-only agents cannot edit files (`disallowedTools`), implementation agents preload the `code-structure` skill, and every agent has a `color` for the subagent view.

### Engineering Core

| Agent | Purpose | Best For |
|-------|---------|----------|
| **Feature Planner** | Three-phase validation before implementation (read-only) | Feature planning, spec validation, edge case analysis, evidence plan |
| **Clean Code Architect** | Quality code with DRY, SOLID and the actions/service split | Feature implementation, refactoring, reusable modules, .NET 10 |
| **DeepDive** | Deep investigation and root cause analysis (read-only) | Bug investigation, performance issues, architectural problems |
| **DeepCode** | Implementation partner for DeepDive | Executing fixes with before/after evidence |
| **Security Vulnerability Scanner** | Security audits (read-only) | OWASP Top 10, agent and tool surfaces, pre-deploy reviews |

### Engineering Specialists

| Agent | Purpose | Best For |
|-------|---------|----------|
| **Backend Architect** | System design, APIs, databases, scaling | API design, schema design, microservices, .NET 10 / Node / Go / Python |
| **Frontend Developer** | Modern web UI, accessibility, performance | Angular 20+ (signals, zoneless, Signal Forms, resources), React, Vue |
| **Mobile App Builder** | iOS/Android, cross-platform apps | React Native, Flutter, native integrations |
| **AI Engineer** | LLM integration, RAG, Claude Agent SDK, MCP | AI features, Claude Code harnesses, agentic workflows |
| **DevOps Automator** | CI/CD, infrastructure, deployment | Pipelines, Kubernetes, IaC, PR checks |
| **Rapid Prototyper** | Fast MVP development | Hackathons, proof-of-concepts, quick validation |

### Design

| Agent | Purpose | Best For |
|-------|---------|----------|
| **UI Designer** | Visual design, component libraries | Design systems, component specs, visual polish |
| **UX Researcher** | User research, usability testing (read-only) | User interviews, journey maps, research synthesis |

## Skills

| Skill | Command | Runs as | What It Does |
|-------|---------|---------|--------------|
| **new-feature** | `/new-feature [task]` | main | Isolates the task in a worktree branched from `origin/main`; scope check against open PRs |
| **plan-feature** | `/plan-feature [description]` | forked Feature Planner | Runs all 3 validation phases and produces an implementation-ready plan |
| **implement** | `/implement [plan]` | main, user-only | Executes the plan task-by-task with Backend Architect + Frontend Developer, auto-runs `/cross-review` |
| **cross-review** | `/cross-review [target]` | main, spawns fable + opus | Dual-model review merged and deduplicated into a combined summary |
| **fix-issues** | `/fix-issues [issues]` | main | Fixes all must-fix and should-fix items from any review |
| **investigate** | `/investigate [problem]` | forked DeepDive | Root cause analysis with a reproduction and handoff |
| **review** | `/review [target]` | forked Clean Code Architect | Single-model code review against Clean Code principles |
| **security-scan** | `/security-scan [target]` | forked Scanner | OWASP Top 10 plus agent/tool surfaces |
| **explore** | `/explore [question]` | forked Explore | Trace flows, map architecture, understand code |
| **code-structure** | `/code-structure` | main | Actions vs service-layer guidance with a migration checklist |
| **evidence-driven-testing** | `/evidence-driven-testing [targets]` | main | Annotated screen recording via the bundled recorder, headless and non-UI paths |
| **before-and-after** | `/before-and-after [before] [after]` | main | Screenshot pair and PR-ready markdown table |
| **ship** | `/ship [title]` | main, user-only | Checks, evidence, commit, rebase, push, PR body, Greptile hand-off |
| **greploop** | `/greploop [pr]` | main, user-only | Loop until Greptile reports 5/5 with zero unresolved comments |
| **greploop-apps** | `/greploop-apps [pr]` | main, user-only | Same loop for PRs over Greptile's file-count limit |
| **unslop** | `/unslop` | applied unprompted | Removes AI tells from commit messages, PR text, docs, comments |

"Forked" skills run with `context: fork` on the named agent, so the diff or investigation never lands in your main context. "User-only" skills carry `disable-model-invocation: true` because they change the repo or push commits.

### User-Level Skill

Copy to `~/.claude/skills/` to make it available across all projects:

| Skill | Command | What It Does |
|-------|---------|-------------|
| **setup-agents** | `/setup-agents [project-type]` | Analyzes the project, recommends agents, copies agents, skills, AGENTS.md and settings, generates the CLAUDE.md section |

## Requirements by beat

| Beat | Needs |
|------|-------|
| Isolate | git; `gh` for the open-PR scope check |
| Prove | Python 3 and `ffmpeg`/`ffprobe` (libx264 + ass filter) for the recorder; Node for the headless Playwright path; `npm i -g @vercel/before-and-after agent-browser` for screenshot pairs |
| Ship | `gh` authenticated; Greptile installed on the repo for `/greploop` |

Run `python .claude/skills/evidence-driven-testing/scripts/evidence.py doctor` to check the recorder toolchain. The recorder's own tests live beside it: `python -m pytest .claude/skills/evidence-driven-testing/tests -q`.

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

Use the provided template, which lists the current frontmatter fields (`tools`, `disallowedTools`, `skills`, `isolation`, `memory`, `effort`, `color`):

```bash
cp agent-template.md .claude/agents/your-domain/your-agent.md
```

## Vendored skills and licenses

| Skill | Source | License |
|---|---|---|
| `new-feature`, `code-structure`, `evidence-driven-testing` | adapted from [michaelshimeles/skills](https://github.com/michaelshimeles/skills) | see upstream |
| `before-and-after` | [vercel-labs/before-and-after](https://github.com/vercel-labs/before-and-after) | PolyForm Shield 1.0.0 (in folder) |
| `greploop`, `greploop-apps` | [greptileai/skills](https://github.com/greptileai/skills); frontmatter gains `disable-model-invocation: true` and an `argument-hint` | MIT (in folder) |
| `unslop` | [cursor/plugins (pstack)](https://github.com/cursor/plugins/tree/main/pstack/skills/unslop) | MIT (in folder) |

The `evidence-driven-testing` tests were moved inside the skill folder; the only change is the path to `scripts/evidence.py`.

## License

See [LICENSE](LICENSE) for details.
