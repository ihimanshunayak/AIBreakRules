# ACC / Reference / Safety

> **Non-negotiable safety rules.** Applies to every task, every workflow, every agent session.
> Rule IDs (`SAFE-R*`) are stable — never reuse, never renumber.

---

## SAFE-R01 — Quality > Speed (Absolute #1)

Time is unlimited (10 minutes, 10 hours, 10 days, 10 years). Quality par compromise **kabhi nahi**.
Before every step: 2-second pause → mentally re-read this rule. No jugaad. No skeleton. No shortcuts.

**Verification:** Every deliverable reviewed against `Output.md` before being declared done.

---

## SAFE-R02 — Read Before Acting (Read-Attestation)

- Never act on a rule, workflow, or file that has not been **read to EOF in the current session**.
- Reading partially (offset/limit) does not count as read.
- Missing/unreadable required file ⇒ fail-fast `AIBR_ERR_SSOT_INCOMPLETE_READ`.

---

## SAFE-R03 — No Guessing / Fail-Fast

- Ambiguous instruction ⇒ **STOP** and ask exactly one focused question.
- Never invent paths, names, versions, or intent.
- Fail-fast never mutates state: no file writes, no commits, no partial scaffolding while blocked.
- Error code: `AIBR_ERR_AMBIGUOUS_INPUT`.

---

## SAFE-R04 — No Auto-Scaffolding

- Files/folders are created **only** per written rules, templates, and explicit instruction.
- Never create "helpful" extras: no placeholder folders, no speculative configs, no empty stubs.
- Structure changes = rule changes; they need the template + registration steps.

---

## SAFE-R05 — Secrets Discipline

- Secrets live in **environment variables** or git-ignored local files — never in code, docs, logs, commits, or chat.
- Never echo, print, or embed a secret value. When checking, show only `<present>` / `<absent>`.
- Detected exposure ⇒ fail-fast `AIBR_ERR_SECRET_EXPOSURE` + rotate the secret.
- `.gitignore` must always cover `.env*`, `_secrets*`, keys, credentials.

---

## SAFE-R06 — Git Safety

- **No force-push.** Ever. Any branch. Any repo.
- **No `git reset --hard`**, no destructive rebase, no history rewriting.
- No deleting branches or tags that are not yours without explicit approval.
- If a push is rejected or a merge conflicts: **STOP and wait** — do not force anything.

---

## SAFE-R07 — Destructive Action Approval

Explicit human approval (per `manifest.json → Approvals`) is required before:

| Action | Example |
|---|---|
| Destructive delete | `rm -rf`, dropping files/folders with content |
| Secret change | rotating, adding, removing credentials |
| Rule deletion | removing any `*-R*` rule or whole Reference file |
| History rewrite | any operation banned by SAFE-R06 |

Error code without approval: `AIBR_ERR_APPROVAL_REQUIRED`.

---

## SAFE-R08 — Responsible AI (Scope Gate)

This repository is **defensive and educational**:

- ✅ Allowed: engineering rules, evaluation methodology, safety lessons, defensive framing.
- ❌ Forbidden: operational attack tooling, jailbreak prompts/payloads, harmful datasets, exploit code, step-by-step misuse instructions.
- Any content derived from security/adversarial research must be framed for **defense/education** with source citations.
- When in doubt: do not include it. Ask.

---

## SAFE-R09 — Attribution & Licenses

- Preserve upstream license notices (e.g., llm-attacks is MIT © 2023 Andy Zou).
- Derived work cites its source (file path + version/commit where known).
- Never re-license or strip attribution from studied material.

---

## SAFE-R10 — Single Source of Truth

- Every rule has exactly **one home**; everything else references it by ID.
- Conflicting documents ⇒ fix the duplicate immediately; do not "average" them.
- `manifest.json` is the SSOT for runtime config; `ACC/Reference/` is the SSOT for rules.

---

## Error Code Index

| Code | Trigger |
|---|---|
| `AIBR_ERR_SSOT_MISSING` | Required rules file not found |
| `AIBR_ERR_SSOT_INCOMPLETE_READ` | Rule file read partially (not to EOF) |
| `AIBR_ERR_AMBIGUOUS_INPUT` | Instruction ambiguous — clarification required |
| `AIBR_ERR_APPROVAL_REQUIRED` | Destructive/risky action without explicit approval |
| `AIBR_ERR_SECRET_EXPOSURE` | Secret detected in code/log/doc/commit |
