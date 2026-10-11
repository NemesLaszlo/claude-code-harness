# Agent workflow

Every task moves through the same six beats. Each beat is backed by a skill in `.claude/skills/`, and the implementation beats delegate to the agents in `.claude/agents/`. This file is tool-agnostic (Claude Code, Cursor, Codex and others read `AGENTS.md`); Claude Code imports it from `CLAUDE.md`. Drop it into a repo with the `.claude/` folder and fill in the repo-specific sections at the bottom.

## Workflow

| # | Beat | Skill | What happens |
|---|------|-------|--------------|
| 1 | **Isolate** | `/new-feature` | Fresh worktree and branch from `origin/main`, scope check against open PRs. Never build on `main`. |
| 2 | **Plan** | `/plan-feature` (medium or large work) or `/investigate` (unknowns first) | Feature Planner validates specs, tasks, and the implementation plan. Small, obvious changes skip this beat. |
| 3 | **Build** | `/implement`, guided by `/code-structure` | Backend Architect and Frontend Developer execute the plan task by task. Actions own the "why/when", a service layer owns the reusable "how". Capture the **before** state while reproducing a bug, before fixing it. |
| 4 | **Review** | `/cross-review` then `/fix-issues` | Runs automatically at the end of `/implement`: fable and opus review independently, findings are merged, consensus items are must-fix. |
| 5 | **Prove** | `/evidence-driven-testing` | Verify with the repo's checks plus runtime evidence: an annotated recording of the live test session, scripted captures when headless, measured numbers for non-UI work. |
| 6 | **Ship** | `/ship`, then `/greploop` when Greptile is installed, `/security-scan` before deploy | Checks, evidence assembly, commit, rebase, push, PR with before/after proof. Loop until Greptile reports 5/5 with zero unresolved comments. Finish by presenting the PR URL. |

Ship-beat notes:

- `/before-and-after` drives the `@vercel/before-and-after` CLI. `--markdown` uploads the pair and prints a PR-ready table; it also accepts existing PNGs, so evidence gathered while developing can be reused.
- In containers or VMs where Chrome fails with "No usable sandbox", set `AGENT_BROWSER_ARGS="--no-sandbox"` for the capture.
- The default upload host (0x0.st) is public. Fine for ordinary UI shots; pass `--upload-url` or `IMAGE_ADAPTER=gist` for anything sensitive.

## Writing for humans

Apply `/unslop` to anything a person will read before you commit, post, or send it: commit messages, the PR title and body, README and doc edits, code comments, and the closing reply. It strips AI tells (em dashes, filler, hedging, chatbot phrases, puffery, bold-label lists) and swaps fancy words for plain ones and passive voice for active. Apply it to text you wrote or changed, not to prose you didn't touch.

## Multi-agent rules

- Never commit directly to `main`.
- One worktree and one branch per task and per agent. Never reuse or modify another agent's worktree, branch, or uncommitted work.
- **Scope check** before starting: skim open PRs' changed files (`gh pr list`, `gh pr diff <n> --name-only`) and look for uncommitted work in shared checkouts. On overlap, stop and ask for direction.
- Never force-push to `main`, and never plain `--force` anywhere. Only `--force-with-lease`, only on your own task branch.
- Resolve lockfile conflicts by regenerating, never by hand-merging.
- Worktrees don't isolate shared resources: confirm a dev-server port answers *your* process before trusting it, and don't run schema experiments against a shared database.
- If a conflict can't be resolved confidently, stop and report instead of guessing.

## Completing a task

`/ship` runs this list. When shipping by hand:

1. Keep changes limited to the assigned task.
2. Run the repo's checks (see *Commands and checks* below).
3. Assemble the evidence captured along the way into before/after pairs.
4. Commit with a clear message, rebase onto the latest `origin/main`, and rerun the checks.
5. Push (`git push -u origin <branch>`; after rebasing an already-pushed branch, `--force-with-lease`).
6. Open the PR. The body explains what changed, how it was tested (every claim backed by evidence), before/after proof, and risks or follow-up work. Run the title and body through `/unslop` before posting.
7. Run `/greploop` (or `/greploop-apps`) until **5/5 with zero unresolved comments**, when Greptile is installed.
8. End by presenting the PR URL.

Do not merge the PR unless explicitly instructed. Keep the worktree until the PR is merged or closed.

## Repo-specific sections

Fill these in when dropping this file into a project. Agents read them to execute the beats.

### Commands and checks

<!-- Exact commands, in order. Example:
- `dotnet build` and `dotnet test`
- `npm run lint` and `npm test`
-->

### Hard invariants

<!-- Security and architecture rules that must never be broken. Example:
- All endpoints require authorization unless listed in docs/public-endpoints.md
- No direct DbContext access outside the Infrastructure project
-->

### Environment quick reference

<!-- Ports, env files, local services, how to seed data. -->

### Local test infrastructure

<!-- Stubs, fixtures, fake providers, what cannot be tested locally. -->

## Skill sources

| Skill | Source |
|---|---|
| `plan-feature`, `implement`, `cross-review`, `fix-issues`, `investigate`, `review`, `security-scan`, `explore`, `ship` | this collection |
| `new-feature`, `code-structure`, `evidence-driven-testing` | adapted from [michaelshimeles/skills](https://github.com/michaelshimeles/skills) |
| `before-and-after` | vendored from [vercel-labs/before-and-after](https://github.com/vercel-labs/before-and-after) (PolyForm Shield 1.0.0, license in folder) |
| `greploop` | vendored from [greptileai/skills](https://github.com/greptileai/skills) (MIT); frontmatter gains `disable-model-invocation: true` so only a person starts the push loop |
| `greploop-apps` | local variant of greploop for huge PRs (MIT); same frontmatter edit |
| `unslop` | vendored from [cursor/plugins (pstack)](https://github.com/cursor/plugins/tree/main/pstack/skills/unslop) (MIT); frontmatter edited upstream-style so agents apply it unprompted |
