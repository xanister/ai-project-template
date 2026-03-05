---
description: Universal rules for all interactions - anti-patterns (no backwards compat, no error swallowing, no suppression), plan-first workflow (BUG/FEAT lifecycle, scope guard, Ship It), GitHub Issues tracking, project-specific conventions
---

# Core Rules

## Critical Anti-Patterns (Hard Rules)

### No Backwards Compatibility

Early-stage development with NO external clients. Before every change:

1. Did I add `@deprecated` tags? -> **DELETE THE CODE INSTEAD**
2. Did I add `// legacy` or `// old` comments? -> **DELETE THE CODE INSTEAD**
3. Did I keep an old interface alongside a new one? -> **DELETE THE OLD ONE**
4. Did I add a fallback for an "old format"? -> **JUST USE THE NEW FORMAT**
5. Did I add optional fields to preserve old behavior? -> **MAKE THEM REQUIRED OR DELETE**

### No Error Swallowing

**Trust the contract. If it crashes, the contract was wrong - fix the contract, not the call site.**

Fallbacks (`|| []`, `catch { return default }`) mask bugs. Let errors propagate.

### No Suppression as a Fix

**Fixing a problem by ignoring it is not fixing it.** Before adding any suppression:

1. Did I add `ignoreDependencies`, `ignoreExports`, or similar config entries? -> **Fix the root cause (wrong import path, missing export, etc.) instead**
2. Did I add `// eslint-disable` or `@ts-ignore`? -> **Fix the code or the type, not the linter**
3. Did I add an `exclude` or `ignore` pattern to hide a failure? -> **Understand why it fails and fix it**

### Director Must Use Named Pool Agents

When the Director needs a specialist (designer, researcher, architect, reviewer, tester, developer, deployer), it **must** use `hire_and_spawn_agent` or `send_to_agent`. The Director must **never** use the Agent tool or any internal mechanism to spawn anonymous, unnamed subagents for pipeline roles.

Anonymous subagents bypass agent lifecycle, produce no message history, are invisible in the agents panel, and cannot be resumed — this violates the pipeline contract.

### One Question Per Turn (STOP Gate)

After calling `send_message` with `message_type: "question"`, **STOP immediately**:

- End all tool calls in the current turn.
- Do NOT send additional messages, ask follow-up questions, or continue workflow steps.
- Wait for the user's reply before proceeding.

In multi-question interviews or triage flows, send one question, STOP, receive the answer, then send the next question in a new turn. Never batch questions in a single turn.

**STOP Gates Are Not Advisory.** When a rule says STOP, it means: end all tool calls immediately. Do not:
- Send a follow-up message in the same turn
- Continue to the next workflow phase
- Make any code changes
- Ask a clarifying question alongside the blocked question

Examples of mandatory stops:
- **Pre-Flight**: unplanned files detected → ask, then STOP
- **Verify step**: dev complete → ask "Ready for Ship It?" → STOP until user explicitly approves
- **Review gate** (large items): plan presented → ask "Does this look right?" → STOP

## Work Item Tracking

Work items (BUG, FEAT, IDEA) live in **GitHub Issues** on your repository. All agents interact via `gh` CLI.

### Labels

| Label | Meaning |
|---|---|
| `type:bug` | Bug report |
| `type:feat` | Feature request |
| `type:idea` | Half-baked idea |
| `state:in-progress` | Claimed and being worked on |
| `state:backlog` | Parked long-term |
| `priority:high` / `priority:medium` / `priority:low` | Priority |

Open issues without `state:*` label are **filed** (ready to pick up).

### Common Commands

```bash
# Replace <owner>/<repo> with your repository slug

# List open items ready to pick up
gh issue list --repo <owner>/<repo> --state open

# List in-progress
gh issue list --repo <owner>/<repo> --label "state:in-progress"

# View an issue
gh issue view --repo <owner>/<repo> <number>

# File a new bug
gh issue create --repo <owner>/<repo> --title "BUG: short description" \
  --body "..." --label "type:bug,priority:medium"

# File a new feature
gh issue create --repo <owner>/<repo> --title "FEAT: short description" \
  --body "..." --label "type:feat,priority:medium"

# Claim (mark in-progress)
gh issue edit --repo <owner>/<repo> <number> --add-label "state:in-progress"

# Add a progress comment
gh issue comment --repo <owner>/<repo> <number> --body "Phase 1 done: ..."

# Close (done)
gh issue close --repo <owner>/<repo> <number> --comment "Fixed: ..."

# Backlog
gh issue edit --repo <owner>/<repo> <number> --add-label "state:backlog"

# Remove a label
gh issue edit --repo <owner>/<repo> <number> --remove-label "state:in-progress"
```

### Issue Body Format

When filing a new issue, use this structure in the body:

```markdown
## Description
What the problem or feature is.

## Plan
**Done when:**
- ...

**Files to change:**
- ...

**Tests:**
- ...
```

For large features, also include: Phases, Risks/dependencies.

## Plan-First Development

Every code change follows this lifecycle. Scale the process to fit the work:

### Small work items (< ~1 hour, single phase)

1. **File** -- Create a GitHub issue with title `"BUG: ..."` or `"FEAT: ..."`, body with description, labels `type:bug`/`type:feat` + priority. GitHub assigns the issue number.
2. **Claim** -- `gh issue edit --repo <owner>/<repo> <number> --add-label "state:in-progress"`. Do this before planning so no other agent picks up the same item.
3. **Plan** -- research first (see Research Before Planning), then edit the issue body to fill in the `## Plan` section. If any agents were called during research, summarize their key findings when presenting the plan to the user.
4. **Execute** -- implement; run tests when done
5. **Verify** -- describe how the user can manually test the feature or bugfix (steps to reproduce, what to look for, expected behavior). If manual testing isn't applicable, explain why. Then ask: "Dev complete. Ready for Ship It, or do you want to review anything first?" **HARD STOP: Wait for user approval. Do NOT proceed to Ship It until the user explicitly says "ship it" or approves.**
6. **Ship It** -- run the full Ship It workflow (only after user approval in step 5)

### Large work items (multi-phase, multi-day, or unfamiliar code)

1. **File** -- Create a GitHub issue as above.
2. **Claim** -- `gh issue edit --repo <owner>/<repo> <number> --add-label "state:in-progress"`.
3. **Plan** -- research first (see Research Before Planning), then edit the issue body with a thorough `## Plan`: Done When, Phases, Files, Tests, Risks. Add a `## Research` comment if findings don't fit cleanly into plan fields.
4. **Review gate** -- present the plan to the user. If any research/designer/architect agents were called during planning, include a brief summary of their key findings before the plan. Ask: "Does this look right before I start?" **HARD STOP: Wait for approval.**
5. **Execute** -- implement phase by phase; run tests after each phase
6. **Verify** -- describe how the user can manually test the feature or bugfix. Then ask: "Dev complete. Ready for Ship It, or do you want to review anything first?" **HARD STOP: Wait for user approval.**
7. **Ship It** -- run the full Ship It workflow (only after user approval in step 6)

For large items, recommend starting a **new session** between Plan and Execute for clean context window. The issue number carries all context forward.

### Research Before Planning

Before writing plan fields, research online. Use web search to look up documentation, best practices, known issues, and design patterns relevant to the planned approach.

Write findings as a comment on the GitHub issue. Skip if the work is straightforward enough that research added nothing.

### Hard Rules

- **Gate**: Do not write code without an active work item (GitHub issue with a filled-in Plan). If the user asks to change code without one, say: "This needs a work item. Should I file a BUG or FEAT first?" Then create the issue and plan before touching code.
- **WIP-1 (per agent)**: Each agent works on ONE work item at a time. Multiple agents can work on different items in the same project simultaneously -- this is intentional. Do NOT block on or ask about other agents' in-progress items. If you discover something new during execution, file it as a separate issue -- do NOT expand the current scope.
- **Phase gates**: After completing each phase, run your test command. If new failures appear, fix them before starting the next phase.
- **Phase progress**: After completing each phase of a large work item, add a comment to the GitHub issue noting what was done, files changed, and test results.
- **No scope creep**: The plan defines the boundary. Work outside the plan = new work item.
- **Trivial exception**: Typos, formatting, one-line config changes can skip the workflow.

### Scope Guard

During execution, actively check whether the current action is within the plan's boundary. Warn the user if any of these triggers fire:

- **File not in plan**: About to modify a file not listed in "Files to change". Say: "This file isn't in the plan. Is this scope creep, or should we update the plan?"
- **Unrelated fix**: The user asks to fix something not covered by the active "Done when". Say: "That sounds like a separate issue. Should I file it as a new BUG/FEAT instead?"
- **"While we're here"**: Adjacent improvement suggested during execution. Say: "That's outside the current plan. Want me to file it separately so we stay focused?"

When warning, always offer three paths:
1. **File it separately** -- create a new GitHub issue for the discovered work
2. **File an idea** -- if half-baked, create an issue with `type:idea` label
3. **Update the plan** -- if it genuinely belongs, edit the issue body to amend the plan first

## Ideas

Ideas are half-baked thoughts captured during development. They are **not work items** and bypass the plan-first workflow entirely.

### Filing an Idea

When the user says "file an idea" (or when an interesting thought comes up during work):

```bash
gh issue create --repo <owner>/<repo> \
  --title "IDEA: short description" \
  --body "Brief description of the idea." \
  --label "type:idea"
```

Continue working -- ideas don't interrupt the current work item.

### Promoting or Discarding

- **Promote**: Idea is solid. Edit the issue to change the title to `FEAT:` or `BUG:`, update the label to `type:feat` or `type:bug`, and fill in a proper plan.
- **Discard**: `gh issue close --repo <owner>/<repo> <number> --comment "Not worth pursuing."`

## Workflows

### Ship It

When the **user** says **"Ship it"** (or explicitly approves shipping), execute ALL phases in order. Never self-trigger this workflow -- it requires explicit user approval from the Verify step.

**Pre-Flight**: Run `git status --short`. Compare modified files against the plan's "Files to change" list (read from the GitHub issue). If any file is not in the plan, ask: "These files are modified but not in the plan: [list]. Stash, revert, or confirm they belong?" **STOP** until resolved. Then run `git pull --rebase origin main`. If merge conflicts, **STOP** and report.

**Phase 1 - Tests**:
1. Analyze changed files -- add/update tests for untested changes.
2. Run your project's test command. Fix any new failures before continuing.

**Phase 2 - Code Quality**:
1. Grep TODO/FIXME/HACK in changed files.
2. Run your project's lint and format commands.

**Phase 2.5 - Review** (for significant changes):
Review the diff against the plan. Check: Does the implementation match Done When? Are there unplanned files? Any obvious issues?

**Phase 3 - Close Work Item**:
Close the GitHub issue with a comment summarizing what was done:
```bash
gh issue close --repo <owner>/<repo> <number> --comment "Fixed: brief description of what was done."
```

**Phase 4 - Commit & Push**:
If ALL prior phases completed with zero errors/failures, commit and push all changes in a single commit.

Commit message format:
- `fix: short description (closes #<number>)` for bugs
- `feat: short description (closes #<number>)` for features

**FALLBACK**: If any phase had errors, warnings, or test failures that couldn't be resolved, **STOP** and report: "Ship It completed with issues: [summary]. Please review before I commit." Wait for user approval before committing.

## Project-Specific Conventions

<!-- Fill in conventions specific to your project's stack. Examples: -->

<!-- ### Adding a Route
[Describe the pattern for adding API routes in your framework]

### Database Migrations
[Describe how to create and run migrations]

### Adding a Component
[Describe the pattern for new UI components] -->
