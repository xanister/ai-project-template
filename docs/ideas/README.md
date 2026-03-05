# Idea Issue Template

Ideas are half-baked thoughts captured during development. They bypass the plan-first workflow — no plan required to file one.

When filing an idea, create a GitHub Issue with the title format `IDEA: short description` and the body below.

**Labels**: `type:idea`

---

## Description

Brief description of the idea. What it is, what problem it might solve, or why it could be interesting. No need to be thorough — capture the thought before it's lost.

---

## Promoting to a Work Item

When an idea is ready to act on:

1. Edit the issue title: `IDEA: ...` → `FEAT: ...` or `BUG: ...`
2. Update the label: remove `type:idea`, add `type:feat` or `type:bug`
3. Fill in a proper Plan section (see `docs/features/README.md` or `docs/bugs/README.md`)

## Discarding

If an idea isn't worth pursuing:

```bash
gh issue close --repo <owner>/<repo> <number> --comment "Not worth pursuing."
```
