# ACC / Reference / Output

> Output quality standard for every artifact: rules, workflows, docs, and (future) code.
> Rule IDs (`OUT-R*`) are stable — never reuse, never renumber.

---

## OUT-R01 — Complete Artifacts Only

- No skeleton, no placeholder, no "TODO: fill later", no length-shortening.
- If a deliverable cannot be completed, say exactly what remains — never stop silently.
- A 5-line stub is not an artifact; it is a defect.

---

## OUT-R02 — Document Structure

Every document follows this shape:

```markdown
# <Title matching filename>

> One-line purpose blockquote. (Scope, if needed.)

---

## 1) ...
```

- H1 matches the filename (minus extension).
- Numbered sections; tables for enumerable facts; code fences for examples/commands.
- Every rule uses the `RULE-ID — Title` heading pattern with Statement / Evidence / Why / Applied or Verification fields.

---

## OUT-R03 — Rule Anatomy (for rule documents)

Every rule carries:

| Field | Content |
|---|---|
| ID + Title | `LLMA-R07 — Deterministic, timestamped output naming` |
| Statement | One-sentence normative rule ("Always X", "Never Y") |
| Evidence | Where the upstream/source proves it (file path, section) |
| Why | The failure mode this prevents |
| Applied | Where this repo adopts it |

---

## OUT-R04 — Citations for Derived Content

- Any rule or fact traced to an external source names it: repo URL + file path (+ version/commit when known).
- Summary over paste: quote only the minimum needed to identify the source; never paste large upstream text.
- License + attribution preserved per `SAFE-R09`.

---

## OUT-R05 — Code File Standards (when code is added)

When this repository gains code, every file must carry:

- Header comment: name, version, purpose, author, creation date.
- Explicit section markers (`# ===== Section =====`).
- Footer comment: usage notes + known limitations.

---

## OUT-R06 — Commit Message Discipline

Format: `<Area>: <what changed and why>`

Examples:
- `Rules: add LLMA-R03 exact version pinning + evidence`
- `Workflow: register RepoStudy in Workflow.Index`
- `Knowledge: add llm-attacks study (defensive extraction)`

One logical change per commit. No mixing rules + tooling + formatting in one commit.

---

## OUT-R07 — Verification Before "Done"

Nothing is "done" until:
- [ ] Files exist at the exact registered paths
- [ ] Cross-references (IDs, paths, links) resolve
- [ ] Rule registration updated (`Workflow.Index.md`, README tables where relevant)
- [ ] No secrets, no skeleton, no unverified claims
