# Rule Template

> Copy this file's *document pattern* into the target rule file. Rules are grouped by domain — a domain file (e.g., `Safety.md`) holds many rules; each rule follows the anatomy below.

---

## Domain File Skeleton

```markdown
# ACC / Reference / <Domain>

> One-line purpose blockquote. Rule IDs (`<PREFIX>-R*`) are stable — never reuse, never renumber.

---

## <PREFIX>-R01 — <Short actionable title>

**Statement:** One sentence. "Always X" / "Never Y" — normative, testable.

**Evidence:** Where this comes from — upstream file path + what it shows.
(For repo-native rules: "Repo decision <date>" or the failure incident.)

**Why:** The concrete failure mode this rule prevents.

**Applied:** Where/how this repo adopts it (path, workflow, or "repo-wide").

---

## <PREFIX>-R02 — ...

```

## Rule Writing Checklist

- [ ] ID sequential (`R01`, `R02`, ...) — never reuse, never renumber (`SAFE-R10`)
- [ ] Title ≤ 60 chars, states the action
- [ ] Statement is one sentence, testable by a reviewer
- [ ] Evidence names a real, inspected source (no "best practice")
- [ ] Why names the failure mode (not "it's better")
- [ ] Applied names the concrete home in this repo
- [ ] Any external source attribution preserved (`SAFE-R09`)
- [ ] Scope gate respected — defensive/educational (`SAFE-R08`)
