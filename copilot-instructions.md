# AIBreakRules — Root Copilot Instructions (Bootstrap)

> **Root boot file.** Every AI-agent session in this repository begins here.
> SSOT chain: **this file → `manifest.json` → `ACC/` → rules apply**.

---

## 0) QUALITY > SPEED (ABSOLUTE #1 RULE)

Time is unlimited. Quality par compromise kabhi nahi. Before every step: 2-second pause, mentally re-read this rule.
No jugaad. No skeleton. No shortcuts. Incomplete work is worse than slow work.

---

## 1) Boot Sequence (strict order)

1. **Read** `manifest.json` — SSOT for identity, language, paths, approvals.
2. **Apply language** from `Developer.Language` (default: `Hinglish`).
3. **Load rules layer** — `ACC/copilot-instructions.md`, then every file in `ACC/Reference/` in this order:
   `Safety.md` → `Output.md` → `Manifest.md` → `Workflow.Index.md` → `Upstream.LlmAttacks.Rules.md`
4. **Load knowledge only when referenced** — `Knowledge/**` is on-demand, never always-on.

If any required rules file is missing or cannot be read to EOF → fail-fast (`AIBR_ERR_SSOT_MISSING` / `AIBR_ERR_SSOT_INCOMPLETE_READ`).

---

## 2) Hard Rules (summary — SSOT is `ACC/Reference/Safety.md`)

- **Read before acting.** Never act on a rule you have not read to EOF this session.
- **No guessing.** Ambiguous instruction ⇒ fail-fast and ask one focused question.
- **No auto-scaffolding.** Files/folders are created only per written rules/templates; structure is never invented.
- **Secrets.** Environment variables only; never in code, logs, docs, or commits.
- **Git safety.** No force-push. No `reset --hard`. No destructive rebase on shared branches. No destructive command without explicit approval.
- **Responsible AI.** This repo is defensive/educational. Never add operational attack tooling, harmful datasets, or exploit payloads. Cite sources; respect licenses.
- **Approvals.** Destructive actions, secret changes, and rule deletions require explicit human approval (see `manifest.json → Approvals`).

---

## 3) Where Things Belong

| Content | Location | Template |
|---|---|---|
| A new rule | `ACC/Reference/<Domain>.md` | `ACC/Template/Rule.Template.md` |
| A repeatable process | `ACC/Workflow/<Domain>/` (4-file pattern) | `ACC/Template/Workflow.Template.md` |
| Long-form study / notes | `Knowledge/<Topic>/` | — |
| New rule domain | new file in `ACC/Reference/` + register in §1 boot list | — |
| A custom agent | `.github/agents/<Name>.agent.md` | — |

**Resident agent:** select **RuleSmith** (`.github/agents/RuleSmith.agent.md`) in the VS Code agent picker for repo studies, rule extraction, rule audits, and Knowledge study docs.

---

## 4) Output Standards (summary — SSOT is `ACC/Reference/Output.md`)

- Complete artifacts only: no TODO placeholders, no "fill later", no length-shortening.
- Every document: H1 title, purpose blockquote, numbered sections, rule IDs where applicable, source citations where derived.
- Every code file (when code is added): header (name/version/purpose/author) + explicit sections + footer with usage notes.

---

## 5) Failure Codes

| Code | Meaning |
|---|---|
| `AIBR_ERR_SSOT_MISSING` | Required rule file not found |
| `AIBR_ERR_SSOT_INCOMPLETE_READ` | Rule file read partially (not to EOF) |
| `AIBR_ERR_AMBIGUOUS_INPUT` | Instruction ambiguous — clarification required |
| `AIBR_ERR_APPROVAL_REQUIRED` | Destructive/risky action needs explicit approval |
| `AIBR_ERR_SECRET_EXPOSURE` | Secret detected in code/log/doc/commit |

On any error: **STOP. Do not mutate anything.** Report the code, cause, and the exact input needed.

---

## 6) Session Checklist (lightweight)

- [ ] Boot sequence completed (§1)
- [ ] Task targets a rule domain or workflow that exists (else fail-fast)
- [ ] No secrets in any output (§2)
- [ ] Deliverable is complete, not skeletal (§4)
- [ ] Commit message states what changed and why
