---
name: setup-agents
description: "Set up the Agent Collection in a new project. Copies relevant agents and skills, the AGENTS.md workflow, settings and gitignore entries, prunes unnecessary ones, and generates the CLAUDE.md agent section. Use when the user says setup agents, add agents to this project, initialize agents, or is starting a new project."
argument-hint: "[project-type] [path-to-agent-collection]"
disable-model-invocation: true
---

## Your Task

Set up the Agent Collection for this project. The user wants to configure agents, skills and the workflow from their collection.

**Project type hint / collection path:** $ARGUMENTS

If the collection path is not given, look for `Agent-Collection` next to this project or under `~/Projects`, and ask if it cannot be found.

### Step 1: Analyze the Project

Understand the current project:
- What's the tech stack? (languages, frameworks, databases)
- What kind of project is it? (web app, API, mobile, monorepo)
- What already exists in `.claude/`? Is there a `CLAUDE.md` or `AGENTS.md`?
- Which tools are available: `gh`, `ffmpeg`, `node`, `python`? (They gate `/ship`, `/evidence-driven-testing`, `/before-and-after`.)
- Is Greptile installed on the repo? (Gates `/greploop`.)

### Step 2: Recommend Agents

Based on the project type, recommend which agents to include. Use these guidelines:

| Project Type | Recommended Agents |
|---|---|
| **.NET + Angular Full-Stack** | Feature Planner, Backend Architect, Frontend Developer, Clean Code Architect, DevOps Automator, Security Scanner |
| **Backend API** | Feature Planner, Backend Architect, Clean Code Architect, Security Scanner, DevOps Automator |
| **Frontend SPA** | Feature Planner, Frontend Developer, UI Designer, Clean Code Architect |
| **Mobile App** | Feature Planner, Mobile App Builder, UI Designer, Backend Architect |
| **Startup/MVP** | Feature Planner, Rapid Prototyper, Backend Architect, UI Designer |
| **Enterprise** | Feature Planner, DeepDive, DeepCode, Backend Architect, Security Scanner, DevOps Automator |
| **AI/ML Project** | Feature Planner, AI Engineer, Backend Architect, Clean Code Architect |

**Feature Planner** is included in every set. Consider adding **DeepDive + DeepCode** to any project where bug investigation is needed.

Present the recommendation and ask the user to confirm or adjust before copying.

### Step 3: Copy Files

After confirmation:

1. Create `.claude/agents/` and copy the selected agent files from the collection
2. Copy the workflow skills to `.claude/skills/`. Always copy: `new-feature`, `plan-feature`, `implement`, `cross-review`, `fix-issues`, `investigate`, `review`, `security-scan`, `explore`, `code-structure`, `ship`, `unslop`
3. Copy `evidence-driven-testing` (with `scripts/`) and `before-and-after` (with `scripts/` and `LICENSE`) unless the project has no UI and no runtime to probe
4. Copy `greploop` and `greploop-apps` (with `LICENSE` and `references/`) only when Greptile is installed
5. Copy `.claude/settings.json` if the project has none; otherwise merge the `permissions.allow` and `permissions.deny` entries
6. Copy `AGENTS.md` to the project root if none exists; if one exists, append the missing sections instead of overwriting
7. Append the gitignore entries: `.claude/worktrees/`, `.artifacts/`, `.claude/settings.local.json`

### Step 4: Generate the CLAUDE.md Section

Add or update `CLAUDE.md` with:
- `@AGENTS.md` import on the first content line
- Active Agents list (with correct relative paths)
- The workflow table (beats, skills, who may start each)
- Agent Collaboration Patterns table (only patterns relevant to included agents)
- Available Skills section (only skills that were copied)

### Step 5: Fill the Repo-Specific Sections of AGENTS.md

Ask the user, or detect from the project, and write:
- **Commands and checks**: exact build, lint and test commands in order
- **Hard invariants**: security and architecture rules
- **Environment quick reference**: ports, env files, local services
- **Local test infrastructure**: stubs, fixtures, what cannot be tested locally

### Step 6: Project-Specific Customization

Ask the user if they want to customize any agents for the project:
- Adjust tech stack references (e.g. Clean Code Architect's .NET section, Frontend Developer's Angular section)
- Add project-specific patterns or conventions
- Modify agent relationships

### Output

Tell the user:
1. What was copied and where, and which optional skills were skipped and why
2. How to use the workflow: `/new-feature` → `/plan-feature` → `/implement` → `/fix-issues` → `/evidence-driven-testing` → `/ship`
3. Which tools to install for the skipped beats (`gh`, `ffmpeg`, `@vercel/before-and-after`)
4. Any agents they might want to customize further
