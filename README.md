# ai-project-template

A GitHub template repository for AI-assisted software projects. Provides a plan-first development workflow, work item tracking via GitHub Issues, and Claude Code rules baked in from the start.

## What This Is

This template gives you:

- **`CLAUDE.md`** — project context and hard rules read by Claude at the start of every session
- **`.claude/rules/core.md`** — detailed workflow rules: anti-patterns, plan-first lifecycle, Ship It checklist, scope guard
- **`docs/`** — work item templates for features, bugs, and ideas
- **`.gitignore`** — standard ignores for Node/TypeScript projects

## Quick Start

1. Click **Use this template** on GitHub to create your repo
2. Clone your new repo locally
3. Edit `CLAUDE.md` — fill in `## Stack` and `## Key Conventions` for your project
4. Edit `.claude/rules/core.md` — fill in `## Project-Specific Conventions`
5. Start coding with Claude Code

## Plan-First Workflow

Every code change follows this lifecycle:

1. **File** — Create a GitHub Issue (`FEAT: ...` or `BUG: ...`) with description and labels
2. **Claim** — Add label `state:in-progress` before planning
3. **Plan** — Research first, then fill in the `## Plan` section on the issue
4. **Execute** — Implement; run tests after each phase
5. **Verify** — Confirm behavior matches the plan
6. **Ship It** — Quality gate → tests → lint → close issue → commit & push

Scale the process to fit the work. Small fixes can compress steps 1-4. Large features should get a review gate after planning.

## Work Item Tracking

Work items live in **GitHub Issues** on your repo with these labels:

| Label | Meaning |
|---|---|
| `type:feat` | Feature request |
| `type:bug` | Bug report |
| `type:idea` | Half-baked idea |
| `state:in-progress` | Claimed and being worked on |
| `state:backlog` | Parked long-term |
| `priority:high` / `priority:medium` / `priority:low` | Priority |

Issue body templates are in `docs/features/README.md`, `docs/bugs/README.md`, and `docs/ideas/README.md`.

## What to Customize

After creating your repo from this template:

- **`CLAUDE.md` → `## Stack`**: List your runtime, framework, database, package manager, test runner
- **`CLAUDE.md` → `## Key Conventions`**: Add naming, file organization, and architectural rules specific to your project
- **`.claude/rules/core.md` → `## Project-Specific Conventions`**: Add stack-specific coding patterns (e.g., how to add a route, how to write a migration)
- **`docs/*/README.md`**: Adjust issue templates to match your team's expectations

## Hard Rules (Do Not Remove)

These rules are universal and must stay in any project using this template:

- **No backwards compatibility** — delete old code instead of deprecating it
- **No error swallowing** — let errors propagate; fix the contract, not the call site
- **No suppression as a fix** — don't `eslint-disable` or `@ts-ignore`; fix the actual issue
- **Plan-first** — every code change needs an active GitHub Issue with a filled-in Plan
- **WIP-1** — one work item per agent; file new issues for discovered work

## Setting Up Labels

When you create a repo from this template, GitHub does **not** copy labels automatically. Run this one-time setup to create the required labels:

```bash
REPO=<owner>/<repo>  # replace with your repo slug

gh label create type:feat         --repo $REPO --color 0075ca --description "Feature request"
gh label create type:bug          --repo $REPO --color d73a4a --description "Bug report"
gh label create type:idea         --repo $REPO --color e4e669 --description "Half-baked idea"
gh label create state:in-progress --repo $REPO --color ededed --description "Claimed and being worked on"
gh label create state:backlog     --repo $REPO --color ededed --description "Parked long-term"
gh label create priority:high     --repo $REPO --color b60205 --description "High priority"
gh label create priority:medium   --repo $REPO --color fbca04 --description "Medium priority"
gh label create priority:low      --repo $REPO --color 0e8a16 --description "Low priority"
```