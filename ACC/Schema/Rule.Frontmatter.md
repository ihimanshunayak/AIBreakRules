# ACC / Schema / Rule Frontmatter

> Schema for **rule documents** in `ACC/Reference/`. Rule *domains* are files; individual rules are heading blocks with stable IDs.

---

## 1) Domain File Header (required)

Every rule domain file (`Safety.md`, `Output.md`, `Upstream.*.Rules.md`, ...) starts with:

```markdown
# ACC / Reference / <Domain>

> <One-line purpose.> Rule IDs (`<PREFIX>-R*`) are stable — never reuse, never renumber.

---
```

Optional (for upstream-derived files) — required metadata block:

```markdown
> **Rules extracted from:** [<repo>](<url>)
> **Source studied at:** commit `<sha>` (<branch>). **License:** <license> — attribution preserved.
> **Scope note:** <defensive/educational statement>.
```

## 2) Rule Block (required anatomy — `OUT-R03`)

```markdown
## <PREFIX>-R<NN> — <Title>

**Statement:** <one normative, testable sentence>

**Evidence:** <source path + what it shows | repo decision date>

**Why:** <the failure mode prevented>

**Applied:** <concrete home in this repo>
```

## 3) ID Rules

| Rule | Detail |
|---|---|
| Format | `<PREFIX>-R<NN>` — 2-6 uppercase letters + `-R` + 2-digit number |
| Sequence | Start at `R01`; increment by 1; **no gaps, no reuse** |
| Stability | Published IDs are immutable; deprecation = keep ID + add `**Status:** DEPRECATED` line |
| Scope | One prefix per domain file; prefix registered in `ACC/copilot-instructions.md` §5 |

## 4) Validation Checklist

- [ ] H1 matches filename
- [ ] Purpose blockquote present
- [ ] Every rule: 4 anatomy fields, non-empty
- [ ] IDs: sequential, unique, prefix-consistent
- [ ] Evidence pointers name inspectable sources
- [ ] Upstream files: license + citation present (`SAFE-R09`)
