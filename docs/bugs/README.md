# Bug Issue Template

When filing a bug report, create a GitHub Issue with the title format `BUG: short description` and the body below.

**Labels**: `type:bug` + one of `priority:high`, `priority:medium`, `priority:low`

---

## Description

What's broken. What the user expected vs. what actually happened.

**Steps to reproduce:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Expected:** [What should happen]

**Actual:** [What happens instead]

**Environment:** [e.g. OS, browser, version — if relevant]

## Plan

**Done when:**
- [ ] [The specific broken behavior is fixed]
- [ ] [A regression test covers this case]

**Files to change:**
- `path/to/file.ts` — [root cause and fix]

**Tests:**
- Regression test: [describe what the test verifies]
- [Any other cases to cover]

---

**Root cause (fill in after investigation):**
[What caused the bug — contract mismatch, wrong assumption, missing guard, etc.]
