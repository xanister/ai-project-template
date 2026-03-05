# [Project Name]

[One-sentence description of the project — what it does and who it's for.]

## Rules

All project rules live in `.claude/rules/`. Read them before planning or coding:

- **core.md** — Anti-patterns, plan-first workflow (BUG/FEAT lifecycle via GitHub Issues), Ship It checklist, scope guard, project-specific conventions. **Read this first.**

## Stack

<!-- Fill in your project's stack. Examples below — replace or remove as needed. -->

- **Runtime**: [e.g. Node.js 22, Bun, Python 3.12]
- **Framework**: [e.g. Express 5, Fastify, Next.js 15, FastAPI]
- **Database**: [e.g. SQLite, PostgreSQL, Redis]
- **Packages**: [e.g. pnpm workspaces, npm, uv]
- **Testing**: [e.g. Vitest, Jest, pytest]. Run: `[test command]`
- **Linting / Formatting**: [e.g. ESLint + Prettier, Ruff]. Run: `[lint command]`

## Key Conventions

<!-- Fill in the conventions that are specific to this project. Examples: -->

- **No backwards compatibility** — delete old code, don't deprecate.
- **No error swallowing** — let errors propagate; fix the contract, not the call site.
- **No suppression as a fix** — don't `eslint-disable` or `@ts-ignore`; fix the actual issue.
- **Plan-first** — every code change needs an active GitHub Issue with a filled-in Plan.
- **WIP-1** — one work item per agent. File new issues for discovered work.
- [Add project-specific naming, file organization, and architectural rules here]

## Development

```bash
# Add your common dev commands here
# install    Install dependencies
# dev        Start dev server
# build      Build for production
# test       Run all tests
# lint       Run linter
```
